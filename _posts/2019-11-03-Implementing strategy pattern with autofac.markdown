---
title: "Implementing strategy pattern with autofac."
date: 2019-11-03 18:49:31 +0100
categories:
  - .NET
tags:
  - C#
  - Design Patterns
  - Autofac
---


When we implement the strategy pattern we usually have a dictionary with an enumerated type that corresponds to the types of strategy to be used as a key and as a value the types that implement the strategy interface that will be implemented by all types of strategy.

In the constructor we create the dictionary and add the strategy types with their corresponding key of the type listed.

In the end we would call the dictionary by accessing the key to the type of strategy we would like to use at runtime.

It would be something like this:

{% highlight c# %}
private IDictionary<TranslationType, ITranslationStrategy> _translationStrategies;

public Application(IIndex<TranslationType, ITranslationStrategy> translationStrategies)
{
    _translationStrategies = new Dictionary<TranslationType, ITranslationStrategy>();
    _translationStrategies.Add(TranslationType.Spanish, new SpanishTranslationStrategy());
    _translationStrategies.Add(TranslationType.French, new FrenchTranslationStrategy());
    _translationStrategies.Add(TranslationType.English, new EnglishTranslationStrategy());
}

public void Run()
{
    Console.WriteLine(_translationStrategies[TranslationType.Spanish].Translate());
    Console.WriteLine(_translationStrategies[TranslationType.French].Translate());
    Console.WriteLine(_translationStrategies[TranslationType.English].Translate());
}
{% endhighlight %}

As we can see, we have to create the dictionary using new to create and instance of each kind of strategy that we want to use, which is a bad practice since we tighly coupled code.

To solve it we can use dependency injection, specifically using autofac we can use Keyed Services solving with an index.

First we would register our types of strategy in our application composition root:

{% highlight c# %}
var builder = new ContainerBuilder();
builder.RegisterType<Application>().As<IApplication>();
builder.RegisterType<EnglishTranslationStrategy>().Keyed<ITranslationStrategy>(TranslationType.English);
builder.RegisterType<FrenchTranslationStrategy>().Keyed<ITranslationStrategy>(TranslationType.French);
builder.RegisterType<SpanishTranslationStrategy>().Keyed<ITranslationStrategy>(TranslationType.Spanish);

return builder.Build();
{% endhighlight %}

Once registered, we can already use it by injecting them into the constructor of the class in which we will use the strategy pattern:

{% highlight c# %}

private readonly IIndex<TranslationType, ITranslationStrategy> _translationStrategies;
       
public Application(IIndex<TranslationType, ITranslationStrategy> translationStrategies)
{
    _translationStrategies = translationStrategies;
}

public void Run()
{
    Console.WriteLine(_translationStrategies[TranslationType.Spanish].Translate());
    Console.WriteLine(_translationStrategies[TranslationType.French].Translate());
    Console.WriteLine(_translationStrategies[TranslationType.English].Translate());
}
{% endhighlight %}

Thanks to Autofac.Features.Indexed.IIndex autofac manage the strategy dictionary which we inject into the constructor of our class.

[Here I leave a github sample][github-sample] which has a console sample project in .net core 3.0 of this implementation of strategy with autofac keyed service lookup.

[github-sample]: https://github.com/perezdecolosia/PlayWithAutofac