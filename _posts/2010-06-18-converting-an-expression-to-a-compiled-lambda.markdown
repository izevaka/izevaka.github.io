---
layout: post
title: Converting an Expression to a compiled Lambda
tags:
- csharp
- expressions
- General
- lamdbas
status: publish
type: post
published: true
meta:
  jd_tweet_this: 'yes'
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  wp_jd_bitly: http://bit.ly/ayr9FH
  wp_jd_target: http://www.somethingorothersoft.com/2010/06/18/converting-an-expression-to-a-compiled-lambda/
---
###Compiling an expression to a lambda
Today I had to convert a binary expression to a lambda and then use in a where clause on a collection. Easy enough, I thought, make an `ExpressionLambda`, compile it, substitute it into the where clause, no worries.

This is the first cut of the code:

<div>
[code lang="csharp"]

public class ClassWithProperties {
	public string Prop { get; set; }
}

static void ExpressionCrashes() {

	var expr = Expression.Equal(Expression.Property(Expression.Variable(typeof(ClassWithProperties)), &quot;Prop&quot;), Expression.Constant(&quot;value2&quot;));

	var props = new[] { Expression.Parameter(typeof(ClassWithProperties)) };
	//Throws &lt;variable '' of type 'SOCSTest.ClassWithProperties' referenced from scope '', but it is not defined&gt;
	var lambda = Expression.Lambda&lt;Func&lt;ClassWithProperties, bool&gt;&gt;(expr, props).Compile();

	var items = new List&lt;ClassWithProperties&gt; { 
		new ClassWithProperties{Prop = &quot;value1&quot;},
		new ClassWithProperties{Prop = &quot;value2&quot;},
		new ClassWithProperties{Prop = &quot;value2&quot;}
	};

	//Should return &lt;&quot;value2&quot;, &quot;value2&quot;&gt;
	items.Where(lambda);
}

[/code]
</div>

The expression is a basic one - it calls an equality comparison on a property off an object and a constant. It is equivalent to this:

<div>
[code lang="csharp"]
value.Prop == &quot;value2&quot;
//Where value is a parameter of type ClassWithProperties
[/code]
</div>

Googling for this exception and just general expression and lambda stuff kinda hints at people having the same problem as I am but with no fast solution. It was Jon Skeet's <a href="http://stackoverflow.com/questions/1574427/lambda-parameter-not-in-scope-while-building-binary-lambda-expression/1574455#1574455">answer to question I didn't understand</a> on StackOverflow that finally put me onto the right track.

In order to compile a lambda from an expression, you need to provide **all instances of `ParameterExpression`** that appear in the expression tree when creating a lambda expressionn (i.e. when calling `Expression.Lambda<T>(Expression, params ParameterExpression[])`). The Expression engine will not match types and names parameters in an expression - as far as it's concerned different instances of `ParameterExpression` are discrete parameters.

Rewriting the above code yields something that doesn't crash:

<div>
[code lang="csharp"]
static void ExpressionWorks() {
	var variable = Expression.Variable(typeof(ClassWithProperties));
	var expr = Expression.Equal(Expression.Property(variable, &quot;Prop&quot;), Expression.Constant(&quot;value2&quot;));

	var props = new []{ variable };
	var lambda = Expression.Lambda&lt;Func&lt;ClassWithProperties, bool&gt;&gt;(expr, props).Compile();

	var items = new List&lt;ClassWithProperties&gt; { 
		new ClassWithProperties{Prop = &quot;value1&quot;},
		new ClassWithProperties{Prop = &quot;value2&quot;},
		new ClassWithProperties{Prop = &quot;value2&quot;}
	};

	//Returns &lt;&quot;value2&quot;, &quot;value2&quot;&gt;
	items.Where(lambda);
}
[/code]
</div>

As you can see, we reused the instance `variable` in both expression declaration and `Expression.Lambda` call. Everything works fine now.

###Getting the parameters from Expression at runtime
My situation was such as that I had to deal with an already created expression. So I couldn't simply take the variable parameters that are used to create an expression and reuse them when creating a lambda. The solution was to traverse the expression tree and get a list of all the `ParameterExpression` instances that are contained in the expression and pass those to `Lambda` function.

Luckily, [`ExpressionVisitor`][1] API provides an easy way to achieve this. All you have to do is inherit from `ExpressionVisitor` and implement one function to trap all the goodies we need:

<div>
[code lang="csharp"]
public class ParameterVisitor : ExpressionVisitor{

	List&lt;ParameterExpression&gt; m_Parameters;

	public IEnumerable&lt;ParameterExpression&gt; GetParameters(Expression expr) {
		m_Parameters = new List&lt;ParameterExpression&gt;();
		Visit(expr);
		return m_Parameters;
	}
		
	protected override System.Linq.Expressions.Expression VisitParameter(System.Linq.Expressions.ParameterExpression p) {

		if(!m_Parameters.Contains(p))
			m_Parameters.Add(p);
			
		return base.VisitParameter(p);
	}
}
[/code]
</div>

We can even create an extension method to hide all complexity of creating a lambda:

<div>
[code lang="csharp"]
public static Func&lt;TSource, TResult&gt; CreateLambda&lt;TSource, TResult&gt;(this Expression expression) {
	var visitor = new Bluecap4.Core.Expressions.ParameterVisitor();
	var parameters = visitor.GetParameters(expression);
			
	return Expression.Lambda&lt;Func&lt;TSource, TResult&gt;&gt;(expression, parameters).Compile();
}
//Usage
static void ExpressionWorks() {
	var expr = Expression.Equal(Expression.Property(Expression.Variable(typeof(ClassWithProperties)), &quot;Prop&quot;), Expression.Constant(&quot;value2&quot;));

	var items = new List&lt;ClassWithProperties&gt; { 
		new ClassWithProperties{Prop = &quot;value1&quot;},
		new ClassWithProperties{Prop = &quot;value2&quot;},
		new ClassWithProperties{Prop = &quot;value2&quot;}
	};

	//Returns &lt;&quot;value2&quot;, &quot;value2&quot;&gt;
	items.Where(expr.CreateLambda&lt;Func&lt;ClassWithProperties,bool&gt;&gt;());
}
[/code]
</div>


  [1]: http://msdn.microsoft.com/en-us/library/bb546136%28v=VS.90%29.aspx