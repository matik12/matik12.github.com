---
layout: post
title: "Jasmine - Run test case multiple times with different input data"
categories: [Jasmine, Unit, Tests, Test Cases]
disqus: true
---

![Jasmine Specification](/images/2017-01-28/jasmine-spec.png)

[Jasmine](https://jasmine.github.io/2.0/introduction.html) is a well-known behaviour driven framework for testing JavaScript code, which is both powerful and helpful when writing unit tests to provide sustainable code base. Quite often during implementation of unit test cases, we need to **cover one particular scenario multiple times** using different input data. Later on, I am going to show simple helper code to do it nice and easy.

<!--more-->

### Define unit test case to run it multiple times

The sample unit test below, shows how to run simple test once. Of course, it is JavaScript, so we can write for loop and describe test - **it** multiple times to run it for different input data using the same scenario.
{% highlight typescript %}
describe('Test example', () => {

    it('Should expect true to be true when true value provided', done => {
        expect(true).toBeTruthy();
        done();
    });
});
{% endhighlight %}

For that use case, I have created simple jasmine utility function, that will iterate through array of input data and create the same test case for each input data. What is more it will pass current input data to the particular test case, so you can use it to actually test the code. The helper function is defined in seperate file - **jasmine-utils.ts**.

{% highlight typescript %}
export default function testCases(values: any[], func: (value: any) => void) {
    for (let i = 0, count = values.length; i < count; i++) {
        func.apply(this, [values[i]]);
    }
}
{% endhighlight %}

The following example shows, how to use **testCases** function to run the same test case multiple times. It's very easy, clean and intuitive.

{% highlight typescript %}
import testCases from 'test/unit/jasmine-utils';

describe('Test case example', () => {

    beforeEach(() => {
        // left empty for mocha reporter, otherwise it groups test suites from other files
    });

    testCases(['first value', 'second value', 5], (value: any) => {
        // before run replace ' with ` to use string interpolation - ' for better syntax highliting
        it('Should run test case using value: ${value}', done => {
            expect(value).toBeDefined();
            done();
        });
    });
});
{% endhighlight %}

You can see the result of running the code above using **karma** and **mocha reporter**. As you can notice, each test case is run separately and report the input data that was provided to test particular scenario.

![Sample test case result](/images/2017-01-28/sample-test-case.PNG)

### Summary

I hope you like my helper and in every day work always remember to unit test your code :) If you have any questions or insights, please leave me a comment below.
