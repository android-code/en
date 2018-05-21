---
layout: post
title: "Singleton"
date:  2018-04-30
categories: ["Design patterns"]
image: singleton
github: Singleton
description: "Design patterns / creational"
keywords: "singleton, pattern, design pattern, design patterns, creational pattern, sharedpreferences, android, java, programming"
---

## Purpose
`Singleton` (structural pattern) is primarily designed to limit the creation of objects of a given class to one instance and provide global access to it. It owes its origin to `C ++`, where it played the role as the successor to the global variable used by the preprocessor. This pattern arouses a lot of controversy - seemingly simple, easy to implement, but badly used is a popular anti-pattern. This happens when its role is reduced to a global variable without considering the use context. `Singleton` can be used where there should be only one shared instance of the class.

## Limitations
`Singleton` is created based on a private constructor that prevents to extend the class. In addition to managing its life cycle, it plays the role of business logic, which violates the principle of `SRP` - a single responsibility. What's more, it breaks the principle of `OCP` - open / closed, which says that classes should be open to extension but closed to modifications. `Singleton` makes testing difficult because before calling the tests, properly initialization of object must be provided. Due to the architecture of the `Android` system `Singleton` is not a recommended pattern in most cases. `Singleton Mutable` can lose its state when the system needs to release the memory resources. Whereas `Singleton Immutable` can often be replaced with `Utility` classes (with static methods).

## Usage
This pattern is used in accessing `SharedPreferences`. Also a popular practice is to implement for objects such as `Logger` or data access APIs, eg: `Retrofit`.

## Implementation
There are many implementations of this pattern. One of the simplest is based on initializing the object through the public `getInstance` method only when it is first called. The constructor is invisible from outside the class.

![Singleton diagram](/assets/img/diagrams/singleton.svg){: .center-image }

The following listing shows the simplest implementation of `Singleton`. However, it causes problems in a multi-threaded environment.

{% highlight java %}
public class Singleton {

    private static Singleton INSTANCE;

    private Singleton() {
    }

    public static Singleton getInstance() {
        if(INSTANCE == null) {
            INSTANCE = new Singleton();
        }
        return INSTANCE;
    }
}
{% endhighlight %}

Double checking and locking should be used for `Singleton` in multithreaded applications.

{% highlight java %}
public class DoubleCheckSingleton {

    private static DoubleCheckSingleton INSTANCE;

    private DoubleCheckSingleton() {
    }

    public static DoubleCheckSingleton getInstance() {
        if (INSTANCE == null)
            synchronized (DoubleCheckSingleton.class) {
                if (INSTANCE == null)
                    INSTANCE = new DoubleCheckSingleton();
            }
        return INSTANCE;
    }
}
{% endhighlight %}

The optimal solution is `Singleton Holder`, which ensures lazy creation of instances and does not require synchronization.

{% highlight java %}
public class SingletonHolder {

    private SingletonHolder() {
    }

    private static class Holder {
        private static final SingletonHolder INSTANCE = new SingletonHolder();
    }

    public static SingletonHolder getInstance() {
        return Holder.INSTANCE;
    }
}
{% endhighlight %}

## Example
The application is designed to store and display user data. Access to data should be possible from different places of application. To solve the problem `SharedPreferences` (which allows to store application settings) can be used. Read access and write access is provided by the class instance, and these operations are independent of this instance. Therefore, there is probably no need to store multiple `SharedPreferences` instances in memory. Access to application settings can be used in many places, therefore the use of the `Singleton` pattern seems to be justified. `SharedPreferences` implicitly uses the `Singleton` pattern. The following listing presents its explicit implementation.

{% highlight java %}
public class SharedPref {

    private static SharedPref instance;
    private static SharedPreferences sharedPreferences;
    private static SharedPreferences.Editor editor;

    private SharedPref() {
    }

    public static SharedPref getInstance(Context context) {
        if(instance == null) {
            synchronized (SharedPref.class) {
                if(instance == null) {
                    instance = new SharedPref();
                    sharedPreferences = context.getSharedPreferences(context.getPackageName(), Activity.MODE_PRIVATE);
                    editor = sharedPreferences.edit();
                }
            }
        }
        return instance;
    }

    public void writeString(String key, String value) {
        editor.putString(key, value);
        editor.commit();
    }

    public void writeInt(String key, int value) {
        editor.putInt(key, value);
        editor.commit();
    }

    public void writeBoolean(String key, boolean value) {
        editor.putBoolean(key, value);
        editor.commit();
    }

    public String readString(String key) {
        return sharedPreferences.getString(key, "");
    }

    public int readInt(String key) {
        return sharedPreferences.getInt(key, 0);
    }

    public boolean readBoolean(String key) {
        return sharedPreferences.getBoolean(key, false);
    }

    public void clearPreferences() {
        editor.clear();
        editor.commit();
    }
}
{% endhighlight %}

## Libraries
The proper use of `Singleton` is the use of the `Dagger 2` framework, implementing the 'Injection of dependencies' pattern.
