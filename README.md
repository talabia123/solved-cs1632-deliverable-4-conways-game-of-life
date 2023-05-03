Download Link: https://assignmentchef.com/product/solved-cs1632-deliverable-4-conways-game-of-life
<br>
For this assignment, your group will profile a Conway’s Game of Lifesimulation, and improve its performance by refactoring several methods (to bedetermined by the results of the profiling). This will consist of severalparts:

1. Profiling (before) to determine which method is the most CPU-intensive2. Adding pinning tests (in the form of JUnit tests) to show that the functionality is unchanged by your modifications2. Refactoring the method to be more performant while making sure your pinning tests still pass3. Profiling (after) showing that your rewrite helped make your method more performant

The code is available under the src/ directory.

## How to Run SlowLifeGUI

1. Running GameOfLife. For Windows do (for running SlowLifeGUI with argument 5):“`run.bat 5“`For Mac / Linux do (for running SlowLifeGUI with argument 5):“`bash run.sh 5“`1. Running the TestRunner on GameOfLifePinningTest. For Windows do:“`runTest.bat“`For Mac / Linux do:“`bash runTest.sh“`

Alternatively, I’ve created an Eclipse project for you so you can use Eclipse to import the existing project.

## What do do

The program is an implementation of Conway’s Game of Life(https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life). You can change thestate of a cell (from living to dead) by clicking on one of the buttons. Cellswhich are currently alive have an X and a red background; cells that are deadnow, but were at any point alive during the current run, will have a greenbackground.

There are several other buttons which invoke different features:

1. Run – this will run one iteration of the Game of Life2. Run Continuous – This will run iterations until you press the Stop button.3. Stop – This will stop the current “Run Continuous” run. It will have no effect if the program is not running continuously already.4. Write – This will write the state of the system to a backup file, to be loaded later.5. Undo – This will undo the previous iteration. It will only work for one iteration (that is, you cannot do multiple undos to get back multiple iterations).6. Load – This will load a previously-saved backup file (created using the Write button) to the current world.7. Clear – This will clear the current world.

The application accepts one command line argument, specifying the size of theworld (e.g., if you enter 10, then you will create a 10 x 10 world).

### Task 1: Profiling using VisualVM

For the purposes of performance testing, we will focus on a 5 X 5 world. Forthe initial pattern, we will use the “blinker” pattern shown in:

https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life#Examples_of_patterns

The actual pattern GIF is at:

https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life#/media/File:Game_of_life_blinker.gif

We will start from the vertical bar on a 5X5 matrix as shown in the GIF.

For an actual full performance test suite, we would have to try multiple worldsizes and multiple patterns but for the purposes of this deliverable, we willfocus on performance debugging only the above scenario. As it happens, once wedebug the above scenario, the application will start running quickly for allscenarios.

There are exactly **THREE** major performance issues with **THREE** methods inthe code. They could be in any feature of the program! I recommend you tryexploratory testing to try out different features to determine which featuresmay have performance problems before profiling the application. There are**TWO** features that have problems (that is, two buttons). The threeperformance problems are dispersed in those two features.

In order to determine the “hot spots” of the application, you will need to runa profiler such as VisualVM (download at https://visualvm.github.io/). Using aprofiler, determine the THREE methods you can modify to measurably increase thespeed of the application without modifying behavior. Refer to Exercise 4 for adetailed explanation of how to use VisualVM to profile an application.

Now there is one more step that you have to do on VisualVM not explained inExercise 4: you need to replace “GameOfLife” with “*” in the “Profile classes:”window on the right before pressing on the “CPU” button to start profiling.This instructs VisualVM to not only the GameOfLife class (the class with themain method), but all classes in the application. You did not need to do thisfor the Exercise 4 MonkeySim application because it was single-threadedapplication. All code in a single-threaded application execute starting fromthe main method, so the default behavior of VisualVM to instrument startingfrom the main method class was just fine. GameOfLife is a GUI application andin a Java GUI application, there are multiple event handler threads running inthe background to handle events like button presses concurrently with theapplication. In this case, the code for these threads do not start from themain method.

### Task 2: Writing Pinning Tests for the Three Methods

Before doing refactoring any method, you should create “pinning tests” (asdescribed in the section on legacy code earlier – please review the slides onWriting Testable Code if you need a refresher). These pinning tests shouldcheck that the behavior of a modified method was not changed by your refactor.The methods should work EXACTLY the same as before, except they should befaster and take up less CPU time. **There should be at least one pinning testper method refactored.**

In general, a pinning test doesn’t necessarily have to be a unit test; it canbe an end-to-end test that you slap on quickly for the purposes of refactoring(without spending the effort to localize tests by mocking external objects).The end-to-end pinning test is then gradually refined into more high qualityunit tests. Sometimes this 2-step process is necessary because sometimes youcannot write high quality unit tests before refactoring to make the code moretestable (e.g. via dependency injection). So you need a temporary end-to-endpinning test to protect the code base meanwhile. For this deliverable, thereis no reason you cannot write unit tests from the get-go for pinning tests asthe dependency injection(s) has already been done for you. An example is the**setCells** method in MainPanel.

Here are some requirements for your pinning tests:

1. You will use the 5 X 5 blinker pattern that I described above when a patternis required:

https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life#/media/File:Game_of_life_blinker.gif

The vertical bar pattern should be your precondition and the next horizontalbar pattern should be your postcondition. **For the postcondition, make sure youcheck all 25 cells in the 5 X 5 pattern**.

1. You are required to localize each pinning unit test within the tested classas we did for Deliverable 2 (meaning it should not exercise any code fromexternal classes). You will have to use Mockito mock objects to achieve this.

1. Also, you may have to use behavior verification instead of stateverification to test some methods because the state change happens within amocked external object. Remember that you can use behavior verification onlyon mocked objects (technically, you can use Mockito.verify on real objects toousing something called a Spy, but you won’t need it for this deliverable). Youwill get point deductions if you don’t use mock objects and behaviorverification appropriately.

1. Note that even though the class is named GameOfLifePinningTest, the methodsyou test will not necessarily come from the GameOfLife class. You willcreate whatever objects from whatever classes are necessary to test the threerefactored methods. Hint: there is no reason for you to create a GameOfLifeobject as there are no methods that you need to refactor there.

You will write all your pinning tests in the class GameOfLifePinningTest bycompleting the TODOs. Please heed the comments. Just like for Deliverable 2,you can add a Java stack trace to the error message to get information aboutwhy your tests are failing by inserting the following line belowTestRunner.java line 24:

“`System.out.println(f.getTrace());“`

### Task 3: Refactor the Three Methods

Now refactor the three methods so that they are no longer performance problems.If you look carefully, the three methods do a lot of wasted work for no reason.It should be easy to refactor my removing that work. Make sure that yourpinning tests pass after refactoring.

## Report Format

Please use the ReportTemplate.docx file provided in this directory to writeyour report. If you don’t have a .docx compatible word processor, that’sperfectly fine as long as you follow the same organization. A PDF version ofthe file is at ReportTemplate.pdf. Please keep the page separation.

The report should have a title page with:* Your name(s)

ON A SEPARATE PAGE, write a brief report on the first feature you optimized.Write the name of the feature, the methods you refactored, and a VisualVMscreenshot of method “Hot spots” *after* the refactoring. Please only includethe “Hot spots” window in the interest of space. Please refer to Exercise 4 onhow the Hot spots window looks like.

ON A SEPARATE PAGE, write a brief report on the second feature you optimized.Write the name of the feature, the methods you refactored, and a VisualVMscreenshot of method “Hot spots” *after* the refactoring. Please take care notto click on any other feature while profiling (including the first feature).

There are two features with performance problems as I mentioned (as in twobuttons). Make sure you include both of them. One feature has two problematicmethods, another feature has one problematic method.

## Grading* Report – 10%* My performance tests pass (autograder) – 45%* My pinning tests pass (autograder) – 15%* Your pinning tests pass (autograder) – 15%* Your pinning tests are written correctly – 15%

Please read grading_rubric.txt before submitting!

Note that 75% of your grade (besides the report) will be graded by GradeScopeautograding. However, adjustments to your autograded score may follow if youmake a bad faith attempt at tricking the autograder (e.g. write a pinning testthat does not properly test the method you are refactoring).

## Submission

You will do one submissions per group for this deliverable as usual.

1. You will create a github repository just for deliverable 4. Make sure youkeep the repository *PRIVATE* so that nobody else can access yourrepository. Once you are done modifying code, don’t forget to commit and pushyour changes to the github repository. When you are done, submit your githubrepository to GradeScope at the “Deliverable 4 GitHub” link. Once you submit,GradeScope will run the autograder to grade you and give feedback. If you getdeductions, fix your code based on the feedback and resubmit. Repeat until youdon’t get deductions.

1. Submit your report to GradeScope at the “Deliverable 4 Report” link.

## GradeScope Feedback

It is encouraged that you submit to GradeScope early and often. Please use thefeedback you get on each submission to improve your code!

The GradeScope autograder works in 3 phases:

1. GameOfLife method performance tests (45.0):

I run performance tests on each of the three methods you shouldoptimize. If any of those methods time out after 10 ms, you get a -15deduction.

1. GameOfLife method pinning tests (15.0):

I run my own pinning tests on each of the three methods you shouldoptimize. These pinning tests pass without you having to do anything(obviously because they are meant to test existing behavior of legacy code).And they should stay that way. If any of the pinning tests fail, you get a-5 deduction.

1. GameOfLifePinningTest method tests (15.0):

I run the pinning tests you wrote (GameOfLifePinningTest) on yourimplementation. If any of the pinning tests fail, you get a -5 deduction.

1. GameOfLifePinningTest Mocking and Behavior Verification (0.0):

This section gives you feedback on whether you did proper mocking andbehavior verification. It does three test runs using yourGameOfLifePinningTest: 1) A test run on a defect-free implementation, 2) Atest run on GameOfLifeBuggy.jar with a bug injected into a mocked object,and 3) A test run on GameOfLifeBuggy.jar with a bug injected into a methodthat can only be tested using behavior verification.

If you did a good job, the bug injected into the mocked object shouldnot cause any additional test failures since mocked object methods are neverexecuted (stubs are executed instead). On the other hand, the bug injectedinto the method tested using behavior verification should result in anadditional failure due to the behavior change. If all goes well, you shouldsee the followimg lines at the end of this section:

“`PASSED (5/5): Bug injected into mocked object did not cause additional test failures.PASSED (5/5): Behavior verification correctly detected change in behavior.“`

If you see FAILED instead, you need to fix your tests.GameOfLifeBuggy.jar is included in the repository if you want to do furthertesting. Try running runBuggyMock.bat and runBuggyBehavior.bat, to executethe JAR file with mocked object bug injection and behavior verificationmethod bug injection respectively (Mac/Linux users can run the *.shversions). If you try the vertical bar blinker patter specified onGameOfLifePinningTest.java, you will immediately see that it is defective.You can also try running your GameOfLifePinningTest on the buggyimplementation using runTestBuggyMock.bat and runTestBuggyBehavior.bat. Youwill see the same JUnit output given on the GradeScope feedback.

Just because you got PASSED for both, it does not mean that you areguaranteed to get points for that rubric item. You may have passed simplybecause you did not yet write the relevant test! So in the end, points willbe assigned through manual grading (hence the 0 points assigned in theautograder). But if you wrote the tests and you see FAILED, then you mostdefinitely have a problem.

## Resources

* VisualVM Download:https://visualvm.github.io/download.html

* VisualVM Documentation:https://visualvm.github.io/documentation.html

Method profiling is not the only thing that VisualVM knows how to do. It canalso profile overall CPU usage, heap memory usage, thread creation/termination,class loading/unloading, Java just-in-time compiler activity, etc. It can alsoprofile heap memory in a detailed way to show which types of objects arefilling the memory and where their allocation sites were. And needless to say,VisualVM is not the only profiling tool out there.

In the unlikely case you can’t find what you are looking for in existingprofilers, you can even write your own profiler using the Java Virtual MachineTool Interface (JVMTI). JVMTI is what was used to build VisualVM.

* Creating a Debugging and Profiling Agent with JVMTIhttps://www.oracle.com/technical-resources/articles/javase/jvmti.html

* JVMTI Referencehttps://docs.oracle.com/javase/8/docs/platform/jvmti/jvmti.html