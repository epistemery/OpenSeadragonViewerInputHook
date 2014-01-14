##OpenSeadragonViewerInputHook

OpenSeadragonViewerInputHook is a plugin for [OpenSeadragon](https://github.com/openseadragon/openseadragon) 1.0.0+
which provides hooks into the user input event pipeline for providing additional behavior and/or
overriding the default behavior.

[See the Demo/Test Site Live](http://msalsbery.github.io/openseadragonimaginghelper/index.html)

###Usage

Download [openseadragon-viewerinputhook.min.js](http://msalsbery.github.io/builds/openseadragonimaging/openseadragon-viewerinputhook.min.js) (or the un-minified [openseadragon-viewerinputhook.js](http://msalsbery.github.io/builds/openseadragonimaging/openseadragon-viewerinputhook.js))

To use the plugin, add **openseadragon-viewerinputhook.min.js** after **openseadragon.min.js** to your site.
This adds the **ViewerInputHook** class to the OpenSeadragon namespace.

A **ViewerInputHook** object can be created and attached (if desired) to an [OpenSeadragon.Viewer](http://openseadragon.github.io/docs/OpenSeadragon.Viewer.html) two ways:


1. Call the addViewerInputHook method on the viewer
2. Create a new ViewerInputHook object, passing a viewer reference in the options parameter (optional)

Both methods return a new ViewerInputHook object (although there's currently no public properties or methods available), and
both methods take an options parameter where the event handlers to be hooked may be specified (see the 'Details' section below).

```javascript
    // Example 1 - Use the Viewer.addViewerInputHook() method to create a ViewerInputHook

    // create an OpenSeadragon viewer
    var viewer = OpenSeadragon({...});
    // add a ViewerInputHook to the viewer
    var viewerInputHook = viewer.addViewerInputHook({ hooks: [...] });


    // Example 2 - Attach a new ViewerInputHook to an existing OpenSeadragon.Viewer

    var viewerInputHook = new OpenSeadragon.ViewerInputHook({ viewer: existingviewer, hooks: [...] });
```

###Details

Event handler callbacks are specified in the hooks property (array) of the options object passed when creating a ViewerInputHook object (see example code below).
Any number of hooks can be specified.

Each hook specification in the array should have three properties - tracker, handler, and hookHandler.

The tracker property of each hook definition can be a reference to any [OpenSeadragon.MouseTracker](http://openseadragon.github.io/docs/OpenSeadragon.MouseTracker.html) instance, 
or one of the pre-defined OpenSeadragon viewer trackers - currently 'viewer' or 'viewer_outer'.

The handler property of each hook definition specifies which MouseTracker handler to hook.
Valid values are:


1. 'enterHandler'
2. 'exitHandler'
3. 'pressHandler'
4. 'releaseHandler'
5. 'moveHandler'
6. 'stopHandler'
7. 'scrollHandler'
8. 'clickHandler'
9. 'dragHandler'
10. 'keyHandler'
11. 'focusHandler'
12. 'blurHandler'

The hookHandler property of each hook definition should be the user-defined event handler callback.  All event handler callbacks have the following signature:

    handlerFunc(event)

The ViewerInputHook class inserts your event hook handler methods in front of any existing event handler methods
so the attached handler will be called first. Additional ViewerInputHook objects can be added on the same viewer/MouseTracker to create a chain of hook methods, 
where the last added handler(s) will be called first.

Your hook event handler methods can control the event handling behavior in one or more of the following ways:


1. Set event.stopHandlers = true to prevent any more handlers in the event handler chain from being called
2. Set event.stopBubbling = true to prevent the original DOM event from bubbling up the DOM tree (all handlers returning false will also disable bubbling)
3. Set event.preventDefaultAction = true to prevent the viewer's default action in response to the event (currently applies to clickHandler, dragHandler, and scrollHandler on the viewer (tracker = 'viewer'))

```javascript
    // Example

    var viewer = OpenSeadragon({...});

    viewerInputHook = viewer.addViewerInputHook({hooks: [
        {tracker: 'viewer', handler: 'scrollHandler', hookHandler: onViewerScroll},
        {tracker: 'viewer', handler: 'clickHandler', hookHandler: onViewerClick}
    ]});

    function onViewerScroll(event) {
        // Disable mousewheel zoom on the viewer and let the original mousewheel events bubble
        if (!event.isTouchEvent) {
            event.preventDefaultAction = true;
            return true;
        }
    }

    function onViewerClick(event) {
        // Disable click zoom on the viewer using event.preventDefaultAction
        event.preventDefaultAction = true;
        event.stopBubbling = true;
    }
```

###Demo/Test Site

The [OpenSeadragonImagingHelper plugin demo/test site](https://github.com/msalsbery/OpenSeadragonImagingHelper) uses 
OpenSeadragonViewerInputHook to monitor cursor position and provide custom click and mousewheel event actions.

The sample code is in [scripts/viewmodel.js](http://msalsbery.github.io/openseadragonimaginghelper/scripts/viewmodel.js).  

###Notes

###TODO...


1. jsdoc documentation
2. Provide hooks on reference strip events
3. Provide hooks on navigator events
