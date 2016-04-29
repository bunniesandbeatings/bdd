<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.

# Feature Tests in Rails

## Tools
* Use SimpleBDD
  *  Cucumber leads to 'early dried' tests
  *  Cucumber leads to conditionals in step definitions
  *  Devs have an unhealthy focus on making step definitions reusable.

# Wet vs. DRY
Test's should not be very dry. *WET* tests allow you to continually learn about your system, and understand the impact of feature changes.

For example. 

0. You have eight tests that describe the user registration process.
0. The progression through the process is dried up in Step Definitions or a helper.
0. You change conditional to show a confirmation dialog and edit the test helper to match.
0. You miss the fact that in one of the other tests, the user should still see the dialog. 

The problem is hidden because the test helper provided no context about the different scenarios.

Some *DRY* guidelines:

* Carefully DRY **backgrounds and stubs**
  * Stubs often need to change because of the test
  * Once DRYed stubs are harder to rework
  * This is true of Factories too
  * If you do use DRY tools for backgrounds, make sure they're flexible (IE. FactoryGirl)
* Carefully DRY Givens
	* Make sure that the state in a given is 'wet' tested. E.g. If the given is 'a user is logged in' there must also be a 'User logs in' Feature.
	* Same rule applies as backgrounds and stubs
* Don't dry up behaviours (When)
  * These are the actions you care about. Don't hide the cause away with indirection!
  * Except:
    * code to drive the system, IE Capybara.
* Don't dry up expectations (Then)

## Feature files
* Write the shortest possible features about the story you implement.
* If all your stories 'travel' through your system
  * They will be slow
  * They will not be focused on the *stand-out* aspects of the feature
  * They will get cumbersome and you'll be inclined to dry up, and thus hide, important details.
  * Tests that rely on a significant proportion of 'travel' will not stand out. If all tests travel, then it's hard to know when a certain must travel.

Instead, establish a background at the start of test and mitigate the risk that the whole story is not played out by:
  * Use a smoke test for important paths through the system
  * Make sure you have a travelling test for your background.	


For example, the following test is focused on the action of getting the "widget app".

```
Feature: 'Downloading our awesome widget app'
Given John is logged in
When he visits the download page
And he downloads the widget app
Then he has our awesome widget app 
```

There is no evidence that he can register and log in to do so. That's ok, so long as a test exists for `A user registers with the site` and `A registered yser can log in`.

It is possible that between `Register` and `Login` and between `Login` and `Download` that something could change and invalidate a test. Perhaps the download link no longer appears on the page you see after login. This is where you do need **travelling** tests.

## Smoke and travelling tests
* Write long 'travelling tests' about the important aspects of the application.
  * Try and run these against production 
    * Actual live site if you have a single important instance
    * Clones or emulations of likely production environments if users install your software.
  * Also run them before committing your code to your main branch.
