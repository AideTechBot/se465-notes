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
