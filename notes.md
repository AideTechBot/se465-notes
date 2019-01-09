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
    
