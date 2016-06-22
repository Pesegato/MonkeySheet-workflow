# Sprite animation controls
On the previous chapter, we saw how to run animations by simply invoking a method. However, the animation might need to be run on certain conditions, or do some stuff mid-animations etc. MonkeySheet provides some help for that.

# MSAction
MSAction wraps an high level action, for example "run", "idle", "shoot". The sprite is supposed to be doing one and only one MSAction at each time. It is not a control, but it contains some lifecycle methods and hooks.

# MSActionMachine
MSActionMachine inherit from AbstractControl (and therefore runs on the update loop), and contains a set of MSAction. The purpouse of MSActionMachine is to run the appropriate MSAction.

# Example

```
public class HitReceived extends MSAction {

    public HitReceived(MSControl msc) {
        this.msc = msc;
    }

    @Override
    public void init() {
        //you might want to notify the MSActionMachine that this animation is running...
        //myActionMachine.hitreceiving = true;
        
        // play the actual animation
        msc.playOnce("hit-received");
    }

    @Override
    protected void msUpdate(float f) {
    }

    @Override
    public void finish() {
        //you might want to notify the MSActionMachine that this animation is running...
        //myActionMachine.hitreceiving = false;
    }
}
```

On this simple class the animation `hit-received` is played. After that the animation is finished, so will the MSAction.

```
public class MyActionMachine extends MSActionMachine {
    @Override
    protected void init() {
//don't forget to add this control to the Geometry!
//also don't forget to add the MSControl to the Geometry!

        initActions(
                new HitReceived(msc)
                //other MSActions
        );
    }

//
    @Override
    protected void msUpdate(float tpf) {
        if (hitReceived) {
            startAction(HitReceived.class);
            hitReceived = false;
        }
    }
```