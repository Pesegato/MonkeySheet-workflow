# Build the spritesheet

You need to pack your sprites on a spritesheet. Requirements:

* all sprites must be square
* the final spritesheet must be square

I suggest using TexturePacker \([https://www.codeandweb.com/texturepacker](https://www.codeandweb.com/texturepacker)\) which is free for basic use.

# Prepare the animation data

This is a simple animation data file:

```
<catalog>
<com.pesegato.goldmonkey.DataBuilder id="anim_tick_duration">
  <Float value="0.033"/>
</com.pesegato.goldmonkey.DataBuilder>
<com.pesegato.goldmonkey.DataBuilder id="monkeyguy-anim-data">
    <String value="monkey-guy"/>
    <Int value="8"/>
</com.pesegato.goldmonkey.DataBuilder>
    <com.pesegato.goldmonkey.AnimationBuilder id="idle">
        <Frame value="0" />
    </com.pesegato.goldmonkey.AnimationBuilder>
    <com.pesegato.goldmonkey.AnimationBuilder id="bend">
        <Frame value="1" />
        <Frame value="1" />
        <Frame value="1" />
        <Frame value="1" />
        <Frame value="1" />
        <Frame value="1" />
        <Frame value="1" />
        <Frame value="1" />
    </com.pesegato.goldmonkey.AnimationBuilder>
    <com.pesegato.goldmonkey.AnimationBuilder id="run">
        <Frame value="8" />
        <Frame value="1" />
        <Frame value="1" />
        <Frame value="1" />
        <Frame value="1" />
        <Frame value="1" />
        <Frame value="1" />
        <Frame value="1" />
    </com.pesegato.goldmonkey.AnimationBuilder>
</catalog>
```

The file is also available here: [https://github.com/Pesegato/MonkeySheetSampleApp/blob/master/assets/GoldMonkey/animations.xml](https://github.com/Pesegato/MonkeySheetSampleApp/blob/master/assets/GoldMonkey/animations.xml)

Explanation of the fields:

| Variable | Meaning |
| --- | --- |
| anim\_tick\_duration | milliseconds duration for each frame |
| monkeyguy-anim-data | We are defining a 'MSContainer' named monkeyguy-anim-data. The 'monkey-guy' is the name of the texture to be used, and 8 is the number of rows and columns of the spritesheet |
| idle | We are defining an animation named 'idle'. It is made up of 1 frame starting at position 0 |
| bend | We are defining an animation named 'bend'. It is made up of 8 frames starting at position 1 |
| run | We are defining an animation named 'run'. It is made up of 8 frames starting at position 8 |

# Write the code

```
public class Main extends SimpleApplication {

    public Main(AppState... appstates) {
        super(appstates);
    }

    public static void main( String... args ) {

        String gmPath[] = new String[]{
                "assets/GoldMonkey/animations.xml"
        };

        Main main = new Main(new GoldMonkeyAppState(false, true, gmPath), new TestAppState());
        main.start();
    }

    public void simpleInitApp() {

    }
}
```

Since MonkeySheet uses GoldMonkey, it has to be used like this. The path of the animation XML must be specified and passed to GoldMonkeyAppState, which must be loaded first in order to initialize the animation data.

!WARNING! Must use a separate AppState for managing the animations, because doing it inside the simpleInitApp won't allow GoldMonkeyAppState to initialize.

# MSContainer

This class is a container of animations. After loading the XML, the animations are available by name.

# MSControl

This is the control for animations, and keep track of the current position of the running animation for our sprite.

# MSMaterialControl

This depends on MSControl and draw the sprite.

On TestAppState, your init will look like this:

```
    protected void initialize(Application app) {
        MonkeySheetAppState msa = new MonkeySheetAppState();
        MonkeySheetAppState.setTickDuration(GM.getFloat("anim_tick_duration"));

        MSContainer container = new MSContainer("monkeyguy-anim-data");
        msa.loadAnim(container, "run");
        msa.loadAnim(container, "idle");
        Geometry geo = MSAction.createGeometry("spatial", 1f, 1f);
        msc = new MSControl("run");
        geo.addControl(msc);
        MSMaterialControl msmc = new MSMaterialControl(getApplication().getAssetManager(), geo, container, msc);
        ((SimpleApplication) getApplication()).getGuiNode().attachChild(geo);

    }
```

* you start the MonkeySheetAppState and set up the speed of anim\_tick\_duration \(can use hard coded values if you prefer;
* you create at least a MSContainer with the values of the XML;
* you load at least an animation on the MSContainer;
* you create a MSControl and attach it to a Geometry;
* you create a MSMaterialControl with a reference to the MSControl

At this point you can run any animation by simply calling   
`MSControl.play(String animation);`

# Set the center of the sprite

Open the sprite with Gimp, reflect the image vertically and hover with the mouse on the center. Take note of the X and Y values and write on the JSON file

`"centerX": 388,`

`"centerY": 319`



