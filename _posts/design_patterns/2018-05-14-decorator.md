---
layout: post
title: "Decorator"
date:  2018-05-14
categories: ["Design patterns"]
image: decorator
github: decorator
description: "Design patterns / structural"
keywords: "decorator, pattern, design pattern, design patterns, structural pattern, android, java, programming"
---

## Purpose
`Decorator` (structural pattern) allows to extend the functionality or modify the state of an existing object without changing its structure during the lifecycle program. It is an alternative to 'inheritance' which happens during compilation unlike to `Decorator`. Grants the rules of `OCP` - open / closed and `SRP` - single responsibility. This pattern is a kind of `Wrapper` for an existing class. An object of base class can be extended by several decorators, thus enabling the creation of many variants of behavior without interfering with the structure of the extended class. This process applies to one object, not all instances of the class. As a result, the structure of classes and inheritance becomes transparent, and the project is open and flexible for future modifications and new extensions.

## Limitations
The risk of using `Decorator` can be a reduntant number of classes. It could be necessary to consider whether the implementation of this template is indeed the best way to solve the problem.

## Usage
`Decorator` is often used in the implementation of interface controls - `View` classes. It can be found also in `I / O streams in Java`. It is implemented where there is a need to create multiple behaviors for objects of the same type depending on where they occur and the use of inheritance is too expensive or unpredictable. An example of this is the process of taking an order for a given product with the option of adding ingredients to it. Using `Decorator` for 5 extensions, just create 5 Decorators. Using "inheritance" (to provide the same number of types) it would be necessary to create 31 classes!

## Implementation
The basic classes of the components `Component1`, `Component2`, etc. and the base class of the decorator `Decorator` extend the abstract class or implement the common interface `Component`. `Decorator` takes a `Component` class object as a parameter. Decorator classes `Decorator1`, `Decorator2` extend the base` Decorator` class. Decorators implement a new functions and extend the behavior of the `Component` base class methods.

![Decorator diagram](/assets/img/diagrams/decorator.svg){: .center-image }

The following listing shows example of implementations of the `Decorator` pattern.

{% highlight java %}
public abstract class Component {

    protected String name;

    public String getName() {
        return name;
    }

    public abstract int getValue();
}

public class Component1 extends Component {

    public Component1() {
        name = "Component1";
    }

    @Override
    public int getValue() {
        return 10;
    }
}

public abstract class Decorator extends Component {

    protected Component component;

    public Decorator(Component component) {
        this.component = component;
    }

    @Override
    public abstract String getName();
}

public class Decorator1 extends Decorator {

    public Decorator1(Component component) {
        super(component);
    }

    @Override
    public String getName() {
        return component.getName() + " + Decorator1";
    }

    @Override
    public int getValue() {
        return component.getValue() + 5;
    }
}

public class Decorator2 extends Decorator {

    public Decorator2(Component component) {
        super(component);
    }

    @Override
    public String getName() {
        return component.getName() + " + Decorator2";
    }

    @Override
    public int getValue() {
        return component.getValue() + 3;
    }
}
{% endhighlight %}

This implemented pattern can be used in the following way.

{% highlight java %}
//base version of 'product'
Component component;
component = new Component1();

//decorating 'product'
component = new Decorator1(component);
component = new Decorator2(component);

component.getName(); //Component1 + Decorator1 + Decorator2;
component.getValue(); //18
{% endhighlight %}

## Example
The application is a sketch of the avatar building process (`Component`) in a computer game. The player develops his character at various stages of the game by gaining strength points and new skills (`Decorator`). Interactions between characters, such as combat, depend on the strength of their profiles. This profile changes over time, and thanks to the variety of skills acquired, the world of characters is diverse. The following listing shows the basic components of the pattern.

{% highlight java %}
public abstract class Avatar {

    public abstract String getDescription();
    public abstract int getPower();
    public abstract void fight();
}

public class Druid extends Avatar {

    @Override
    public String getDescription() {
        return "Druid";
    }

    @Override
    public int getPower() {
        return 10;
    }

    @Override
    public void fight() {
        //use magic against opponent
    }
}

public class Knight extends Avatar {

    @Override
    public String getDescription() {
        return "Knight";
    }

    @Override
    public int getPower() {
        return 30;
    }

    @Override
    public void fight() {
        //use full of power against opponent
    }
}
{% endhighlight %}

The hero gains additional strength points, powers, and also changes the way of fighting thanks to the help of the following decorators (profiles).

{% highlight java %}
public abstract class AvatarProfile extends Avatar {

    protected Avatar avatar;

    public AvatarProfile(Avatar avatar) {
        this.avatar = avatar;
    }
}

public class Defender extends AvatarProfile {

    public Defender(Avatar avatar) {
        super(avatar);
    }

    @Override
    public String getDescription() {
        return avatar.getDescription() + " Defender";
    }

    @Override
    public int getPower() {
        return avatar.getPower() + 15;
    }

    @Override
    public void fight() {
        //use shield to protect yourself
    }
}

public class Fighter extends AvatarProfile {

    public Fighter(Avatar avatar) {
        super(avatar);
    }

    @Override
    public String getDescription() {
        return avatar.getDescription() + " Fighter";
    }

    @Override
    public int getPower() {
        return avatar.getPower() + 25;
    }

    @Override
    public void fight() {
        //use sword to attack an opponent
    }
}

public class Magician extends AvatarProfile {

    public Magician(Avatar avatar) {
        super(avatar);
    }

    @Override
    public String getDescription() {
        return avatar.getDescription() + " Magician";
    }

    @Override
    public int getPower() {
        return avatar.getPower() + 5;
    }

    @Override
    public void fight() {
        //cast spells to be invisible
    }
}
{% endhighlight %}

The scheme of acquiring new skills and interactions between characters (spread over time) could look like the following list.

{% highlight java %}
Avatar john = new Knight();
Avatar katy = new Druid();

john = new Fighter(john);
john = new Defender(john);
katy = new Magician(katy);

john.getDescription(); //Knight Fighter Defender
katy.getDescription(); //Druid Magician

john.fight();
katy.fight();
{% endhighlight %}

## Libraries
Due to the simplicity of the pattern, its specificity and low cost of implementation, the use of libraries implementing the `Decorator` pattern is pointless. The most common use in `Android` is extending views behavior. `Decor` is an example of a library that makes it easy to add decorators to `View` classes.
