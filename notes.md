# SOFTWARE TESTING - by PLAM

#### Fault

#### Error

#### Failure

Train track example:

```
-----------
|||||||||||
--------------------------
           |||||||||||||||
           ---------------
```
This track is an example of a fault, no train has passed yet. Nothing bad has happened

If there is a train, there is an error, it is in a bad state, but nothing bad has happened

If the train derails, that is a failure.

**Causes:**
- Poor design
- Mechanical stresses (eg. earthquake)

---

Let's write some code:
```
public static numZero(int[] x) {
  //effects if x is null throw NullPointerException
  //otherwise, return number of occurences of 0 in x
  int count = 0;
  for(int i = 1; i < x.length, i++) {
    //program point(*)
    if(x[i] == 0) count++;
  }
  return count:
}
```

State for the example includes:
- **x**
- **i**
- **count**
- **PC**

A wrong state would be:
- **x** = {2,7,0}
- **i** = 1
- **count** = 0
- **PC** = `(*)`

The expected state would have **i** = 0. However this input does not expose a failure - error state is transient.
Only input starting with 0 (eg: `{0,2,7}`) does reveal a failure.

### RIP Fault Model
To get from a fault to a failure:
1. Fault must be **reachable**. (Not code that never executes)
2. Program state following fault must be incorrect (**infection** state)
3. Infected state must **propagate** to the output, causing a failure.

---

## Dealing with faults, erros, and failures
- Fault Avoidance
- Fault Detection 
- Fault Tolerance

### Fault Avoidance
- Social (no 3 AM coding sessions)
- IDE/Tool support to detect faults
- Languages are sometimes immune to some classes of errors (eg. no memory errors in garbage collected languages)
- System design to avoid faults (eg. vending machines)

### Fault Detection
- Testing most applicable here (Automated test suites)
- Can fix the fault one detected
- Maybe roll back
- Alerts(Automated + Customer service)
- Post mortems + evaluations of trends

### Fault Tolerance
- Replicas/Backups/Isolation
- Make tolerant to harware/network failures
- Crisis management teams or PR
- Redundancy: extra hardware
- Isolation: check preconditions

## Testing vs Debugging
#### Testing:
Evaluating software by observing its execution.
#### Debugging:
Finding and fixing a fault given a failure.

## Testing Example
```
if(x-100 <= 0)
  if(y-100 <= 0)
    if(x+y - 200 == 0)
      crash();
```
Sometimes you can't just find the bugs by brute force or fuzzing you really need to understand the code to figure it out.
```
static public int FindLast(int[] x, int y) {
  /* bug: loop test should be i >= 0 */
  for (int i = x.length - 1; i > 0; i--) {
    if (x[i] == y) {
      return i;
    }
  }
  return -1;
}
```
1. Identify and fix the fault
2. If possible, identify a test case that does not execute the fault
    
    `x = null, y = 3`
3. If possible, identify a test case that executes the fault, but doesn't result in an error state.
    
   `x = [3, 1, 2], y = 2 -> result = 2`
4. If possible, identify a test case that results in an error, but not a failure.
    
    `x = [1, 2, 3], y = 5 -> result = -1`
    
    Error is that `i = 0`, iteration never happens, pc never reaches line 4 with `i = 0`.
5. For the given test case, identify the first error state. Describe the complete state.

---
```
class LineSegment:
  def __init__(self, x1, x2):
    self.x1 = x1, self.x2 = x2

def intersect(a,b):
  return (a.x1 < b.x2) & (a.x2 > b.x1)
```
**Note: must document whether endpoints are included!!**    

So lets make a Python unit test:
```
def test_aAbB(self):
  a = LineSegment(0,2)
  b = LineSegement(3,7)
  self.assertFalse(intersect(a,b))
  self.assertFalse(intersect(b,a))
```
* Satisfies statement coverage(trivially) + branch coverage
#### Other ways of generating test:
* Be sure to cover all outputs (or at least all classes of outputs)
* Generate cases randomly:
  - but then you neeed to know expected output to verify pass or fail
  - and it's also hard to hit known interesting points in the problem space
* Cover all values of logical sub-expressions
* Cover all interesting combinations of the input classes

### Sketch of proof of correctness of `intersect`

Rename inputs to:
- `a.x1 = a`
- `a.x2 = A`
- `b.x1 = b`
- `b.x2 = B`

Assume all points distinct(then check assumption)

Write cases:
- `a=A, c=b, a=B`
- `b=a, b=A, b=B`

Assume `a < b` (and then swap them)

Assume `a < A` and `b < B` (Contstructor should reject violating lines?)

Have reduced input space to permutations:
```
aAbB:  .-----.  .------.
abAB:  .------.
            .-------.
abBa:  .---------------.
            .------.
```

### Static Testing ("ahead of time")
- Linters, other compiler-like tools
- Human code review
### Dynamic Testign ("at run-time")
- Unit tests
- Observe program behaviour by executing it

### "I don't want to talk about it"
- Complete testing
- Exhaustive testing
- Full coverage
Please be more precise.

### Test Suite Design - when to stop?
- Out of time - need to move on.
- Open ended exploratory testing 
- Automatic input generation
- Close enough to being exhaustive(along some dimension)
- Have enough statements, branches, program states, use cases

#### Test Cases
Contains inputs and expected outputs.

**Key notions:** 
- Observability - know what the system is doing/what is the internal state of the system
- Controlability - Able to feed inputs into the system

#### Coverage
Identify a space related to the program and cover that space.

- Test requirement - an element of an artifact that a test case must cover

### Working with TR's
- Formulate a set of TRs, depending on the system.
- Ensure that some test case meets each.
**Example:**
- Cover all decisions(branches) in a program
- Each branch creates two TRs (one for true, one for false)
- Cover all methods in a program(each method is one TR)
---
### Infeasible test requirements
Some test requirements are impossible to fulfill
**Example**: Statement converage is infeasible for many programs

Also code like:
```
if(false) {
  unreachablecall();
}
```
or
```
while(0) { local_irq_disable(); }
```
can't be tested.

#### Definition:
Given a set of test requirements `TR` and a test set T, the **coverage level** is the ratio of the # of test requirements satisfied by `T` to the size of `TR`.

**Example:** My test suite achieves 80% statement coverage on this program.

### Exploratory Testing
- Poke around a system to find bugs
- You might do hallway usability testing
Exploratory testing is simultaneous learning, test design, and test execution vs scripted testing (ahead of time/design)

### When is exploratory testing good?
- Not yet clear what right answer is to the test
- Don't understand the system
- Evaluating criteria which are difficult to quantify(eg: Usability)
### When do you want more scripts/automation?
- As you better understand the system you should automate
- Need to run the same tests over and over
- Regression tests
### Exploratory Testing Process
1. Start with a charter for the testing activity.
**Example:** Analyze the product elements of the software
2. Decide what area of software to test.
3. Design a test (informally).
4. Execute the test, log bugs.
5. Repeat 2-4.

#### Notes
- Don't produe exhaustive notes.
  - Take brief notes, enough to reproduce the bug.
- Output at least a set of bug reports + maybe test notes(overall impressions, summary of the strategy/thought process)
- Artifacts, test data/materials
