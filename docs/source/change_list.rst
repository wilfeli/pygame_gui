.. _change-list:

Change List
===========

A record of changes between versions of Pygame GUI.

**Version 0.5.7** - Hiding and better pygame 2 support
-----------------------------------------------------------------


Major Features
...............................

- **show() & hide() feature added to all elements**. Allows you to temporarily hide and show a UIElement or UIWindow rather than having to kill() and recreate it each time when you want it out of sight for a bit. This feature was contributed by @ylenard so all thanks goes to them.

- **switch to using premultiplied alpha blending for pygame 2** - For a long while now features like rounded corners have not worked correctly with pygame 2. Thanks to some recent improvements in the latest version of pygame 2.0.0.dev10 pygame_gui has been able to switch to using pre-multiplied alpha blending when dev10 is also installed. This resolves all the visual issues with rounded corners and I think runs a teeny bit faster too.


Minor features
...............................

- **enable() & disable() have been added to many more elements and windows** - Maybe all of them now, even where it doesn't really make sense. Disable things to your heart's content.
- **focus sets** - This is a new concept I'm trialling in the UI to indicate a group of elements that together constitute a thing that should all have interaction focus at the same time. So far it's working fairly well and has made it easy to extend pygame 2's scrollwheel functionality so that you should now scroll the content of what you are hovering with the wheel (at least in most cases). In the future this idea may make it easier to handle keyboard only input and input via controllers.
- **class IDs for UIElement objects** - UIelement objects could always have an Object ID, but those were designed to be unique specifiers for events as well as theming and sometimes you want to pick out a specific group of elements for theming that all already have unique object IDs. Enter class IDs, there is a new datatype 'ObjectID' that you can pass when you create an element and it lets you set two string IDs, the old unique `object_id` and the new `class_id`. Once you have some objects sharing a `class_id` you can theme theme in a theme file theming block the same way you would with an object ID.  It's also worth noting here that you can load multiple theme files into a single UIManager if you want to organise your theme data some more.

Dropped compatibility
..............................................

- **No longer supporting pygame 1.9.3 & pygame 1.9.4** - Keeping up with the bugs in these old versions of pygame was holding back the GUI so I made the decision to drop support in this version. If you are still using pygame 1.9.3 or 1.9.4, my apologies.

Bug Fixes & Other Changes
.........................................................

 - **Switched to using a custom Sprite and SpriteGroup class as base for UI elements*** - previously I was using the pygame classes but after getting up close and personal with them recently I realised that the existing sprite base was doing things that we weren't using and that a slimmed down sprite could speed things up. In my tests on windows this has made the draw loop about 10% faster.
- **A series of fixes to the drop down menus** - they should now not break when they would have overlapped previously and correctly set the height of the background when the height of a list item is set to a custom value. Thanks to all the people who submitted bugs with these.
- **fixed a bunch of LGTM alerts** - gotta have that A+ rating.


Further thanks
.............................

- Thanks once again to @ylenard for all their hard work put into this release.
- Thank you to everyone who reported issues in the GUI this time around. If you don't report 'em, we can't fix 'em.


**Version 0.5.6** - Loading changes & minor optimisations
-----------------------------------------------------------------


Major Feature
............................

- **Improved loading system** - Pygame GUI now supports:
    - **Incremental loading** - By passing in a loader you create yourself to the UIManager, you can get progress updates on how your GUI resources are loading. See `IncrementalThreadedResourceLoader` in `pygame_gui.core`, or the new loading examples in the [examples repository](https://github.com/MyreMylar/pygame_gui_examples).
    - **Loading resources from python packages** - This is, probably, the wave of the future for python projects. Instead of putting your resources in plain old directories and using boring file paths you can now add an exciting empty dunder `__init__.py` file to your resource directories, transforming them into packages which can then be loaded with a similar style to how we import code. There is a new `PackageResource` class at module scope to support this and some new ways to specify resources in theme files. See the [examples](https://github.com/MyreMylar/pygame_gui_examples) for a few usages and the [documentation](https://pygame-gui.readthedocs.io/en/latest/theme_reference/theme_button.html).
    - **Loading with threads** - As always with anything parallel, this comes with an extra frisson of danger. But in theory you should be able to see some improvement in how fast your resources are loaded. On my hard drive I've seen something like a 10% loading speed increase in my tests, but that can increase to almost 2x faster if your drive access speed is slow - as I discovered loading from a network drive. Care should probably be taken not to try and use any of the resources *while* they are being loaded as heck know what pygame will make of that. Threaded loading is enabled by default, so let us know if any problems crop up and I'll implement a fall-back, sequential-loading-only loader.

Breaking interface change
.....................................................

If you have any code that looks like this:

    background.fill(manager.ui_theme.get_colour(None, None, 'dark_bg'))

Or

    background.fill(manager.ui_theme.get_colour([], [], 'dark_bg'))

Then you will now have to change it to:

    background.fill(manager.ui_theme.get_colour('dark_bg'))

This actually resulted from general optimisation changes but I think it is a solid improvement to the interface for getting default colours from a theme so I am enforcing it.

- **Custom UI elements** - If you've made any custom UI element classes (inheriting from UIElement) with their own theming then the procedure for getting theming IDs and theming parameters has changed slightly. You can see an example of adapting to these changes in the [pygame_paint repository here](https://github.com/MyreMylar/pygame_paint/commit/c5e7023bd0998b461b574f816b033dcf193399d3)

Bug Fixes & Other Changes
.........................................................

 - The speed of creating 100+ buttons in a single frame should now be slightly faster than the 0.4.0 era of Pygame GUI rather than 3x *slower* (fix for #91)
 - Mildly improved exception handling internally - This is an ongoing project.
 - Abstract interface classes now properly enforce their interface on inheriting classes. Oops.


**Version 0.5.5** - The Windows Update, Update
-----------------------------------------------------

No major features, just a smattering of bug fixes, a few new elements and probably some new bugs.

New Elements
............

 - **UIHorizontalScrollBar** - Just like the vertical scroll bar, but in the x axis.
 - **UIScrollingContainer** - Another type of ContainerLike element. this one is largely invisible except for scroll bars that appear on the right hand side and at the bottom when the content inside the container is larger than the container itself.

Minor Features
..............

 - UIFileDialog has a couple of new options on creation mainly to support make file dialogs for loading and saving files. Probably still more bugs to find in this bad boy.
 - New simple method to set the title of a window.
 - New events for when text is changed in a text entry event, when a button is 'clicked once' (pushed down, but not yet released) to match the double click event and when buttons are hovered and unhovered.

Bug Fixes & Other Changes
.........................

 - Added more interfaces to the code base which should make autocomplete more reliable when using the methods of the library.
- Fixed a bug with containers not using 'hover_point()' method for testing hovering collisions with the mouse thus messing up various interactions slightly.
- Fixed a bug with removing the close button on a window theme not correctly resizing the title bar.
- Changed UIElement to take a copy of passed in rectangles in case they are re-used elsewhere.
- Fixed  bugs in UIPanel and UISelection list where anchors and containers of the element were not being copied to their root container leading to shenanigans.
- Resizing the elemnet container for the UIWindow element was missing off the border leading to overlaps. This is now fixed.
- Fix for elements owning root containers anchored to the top and bottom of containers having their root containers incorrectly resized before they were positioned, thereby causing a mess of appearance bugs. It was a bad scene. Should now be fixed.


**Version 0.5.1**
--------------------

Bug Fixes
----------

- Getting the library working with pygame 1.9.3
- Removing window's title bar now works correctly.


**Version 0.5.0** - The Windows Update
--------------------------------------

Major system features
.....................

 - **Big UIWindow class refactoring**. UIWindow features like dragging windows, title bars and close buttons added as core
   features of the class. The class has moved from 'core' submodule to the 'elements' submodule. You can now create
   usable UIWindows without inheriting from the class first.
 - **Windows now support dynamic user resizing**. You can grab corners and sides of windows and stretch them around.
 - **Layout 'anchoring' system**. For laying out UI elements inside Containers (including Windows & Panels). This lets users place
   elements relative to other sides of their containers not just the default 'top left' every time.
 - **Button state transition 'cross-fade' effect.** A bit of flash.
 - **Theming files now support 'prototype' blocks.** To help reduce repetitive styling data. Theming parameter
   inheritance has also been changed to be more generous - e.g. now if you theme the 'button' block it will also affect
   buttons inside windows unless they have a more specific theming block.

New Elements
............

 - **UISelectionList** - a list of elements that let users select either one, or multiple items on it depending on how
   it is configured.
 - **UIPanel** - A new type of Container like element that you can place other elements inside of and set to start
   drawing at a specific layer in the UI. Designed for HUDs and the like.

New Windows
...........

 - **UIConfirmationDialog** - A Dialog Window which presents a choice to users to perform an action or cancel it.
 - **UIFileDialog** - A Dialog that helps users navigate a file system and pick a file from it.
 - **UIColourPickerDialog** - A Dialog window that lets you pick a colour.

Minor Features
..............

 - Drop down menu now supports larger lists of items in smaller space using a scroll bar and a parameter at creation to
   limit the vertical size. By default it will limit it's expansion to the boundaries of the container it is insider of.
 - Drop downs can now be expanded by clicking on the selected item button as well as the little arrow.
 - New theming options to remove the arrow buttons from horizontal sliders and vertical scroll bars.
 - Layer debug function on the UI Manager that lets you inspect what's going on with the UI Layers.
 - You can now set UIPanels and UIWindows as the 'container' parameter for all UIElements directly on creation.
 - Lots of new UI events to support the new elements and a new one for when the horizontal slider has moved.

API Breaking changes
....................

 - Lots of stuff with UIWindow. It's moved submodules, it has lots of new features that previously had to be provided in
   sub classes or didn't exist anywhere. The container for elements now excludes the title bar, shadow and borders of
   the window. Adapting is largely a case of deleting code, but it's a job of work.
 - UIMessageWindow has also changed a lot, it's now themed by it's object ID '#message_window' rather than an element
   ID like before, and it has lost lots of code to the underlying UIWindow class.
 - Object IDs for UI Events have changed to be the most specific ID that can be found or the element that generates
   them. This means code that was checking previously for '#my_window_ok_button' will probably need to be changed to
   check for '#my_window.#my_window_ok_button' or, you could change the button object ID to make it something like:
   '#my_window.#ok_button' because that identifier will now be more unique which was the general goal of the change.
 - Theming files may not perform exactly the same way they did before. Again, you can probably do lots of deleting if
   you make use of the prototype block system and I've tried to keep it mostly the same.
 - Default parameters have changed for 'text_box' and 'button'.

I try to minimise API breaking changes, but before we hit 1.0.0 I'd rather make changes that improve the overall module
than skip them and preserve an API that isn't working anymore.

Bug Fixes & Other Changes
.........................

 - Images loaded by the theming system should now work in pyinstaller -onefile .exe builds.
 - Drop down element should update the selected_option variable upon picking an option.
 - set_position, set_relative_position and set_dimensions methods should now work much more consistently across all
   elements.
 - Text boxes should expand correctly when the appropriate dimension is set to -1 or when the 'wrap_to_height' parameter
   is set to True on startup.
 - Text entry line text selection is smoother now.
 - UIContainer class now used all over the place - replacing the old 'root window' as 'root container', inside sliders &
   scroll bars.
 - Lots of refactoring to please Python Linting tools flake8 and pylint. Always more work to do here, but the code
   should be a few percent cleaner now.
 - Made use of interface/ABC meta classes to remove bothersome circular dependency problems.
 - More tests. Always more tests.
 - Text line documentation bug fixed by contributor **St3veR0nin**
