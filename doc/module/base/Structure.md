# Base/Structure

## Definition

This library provides utility classes for defining the basic structure of the 
document.

## Usages

The `Base/Structure/Container` library defines an optional containing element 
class `.container` for all (or part) of site content, including configurable 
padding and an optional maximum width property.

The `Base/Structure/Row` library defines a float-clearing element `.row`
intended to contain a set of panel items from `Base/Structure/Panel`, although
it could be used for other things - though it is roughly equivalent to 
`.clearfix` except by semantic meaning, so in most cases, other uses should be
relagated to `.clearfix`. This element does not specify any width constraints 
but instead relies on its containing element, which in many cases is 
`Base/Structure/Container` (the `.container` class).

The `Base/Structure/Panel` library provides a set of classes `.panel-1` through
`.panel-X`, where `X` is the number of panels set in the configuration of the
WebBlocks build. The default number of panels is 12. Panels are intended to be
used within a `.row` element, and sum of all `X` values for the `.panel-X` 
elements within a `.row` should not exceed the number of grid panels configured 
for the build. If they do, then the panels will overflow, but gutter behaviors
will not be optimal.

## Features

### Container

The `.container` element offers a simple way to wrap main body content. It is
not intended to be used as an interior element, nor is its behavior defined if
a `.container` is placed within another `.container`.

The container has two configurable properties with the following defaults:

```css
$structure-container-gutter:    2%;         /* must be a percentage */
$structure-container-width-max: 1024px;     /* pixels or false for no max */
```

The behavior of the container is defined as follows:

* Below `$breakpoint-small`, it will have a fixed padding equal to the 
percentage specified by `$structure-container-gutter` when the container is of 
a total width equal to `$breakpoint-small`. This ensures that the gutter will
never completely vanish.
* Between `$breakpoint-small` and `$structure-container-width-max`, it will be
the width of its containing element (usually the window width) with the 
exception of padding on both the right and left sides equal to the gutter 
percentage specified as `$structure-container-gutter`.
* If `$structure-container-max-width` is not false, then the container will not 
grow to beyond the maximum pixel width (gutters included in this width) as
specified by this variable. When the container hits this limit, it is aligned
in the center, holding this maximum width.

### Rows and Panels

While broken into two modules, the `.row` and `.panel-X` classes are generally 
intended to be used in conjunction. Panels are similar to the column pattern
used in conventional grid systems, but are semantically different in that
they are responsive and will go inline below a certain threshold viewport.

Panels have several configurable properties with the following defaults:

```css
$structure-panels:              12;
$structure-panel-gutter:        2.5641%;
$structure-panel-width:         ((100% - ($structure-panels - 1) * $structure-panel-gutter)
                                / $structure_panels);
```

The `$structure-panels` variables defines the number of equal-width panels that
will be generated. In the default case, this means that one may use `.panel-1`
through `.panel-12`. In such an arrangement, `.panel-1` is 1/12 the width of 
`.panel-12`, and `.panel-6` would be 1/2 the width of `.panel-12`. 

The variable `$structure-panel-gutter`, meanwhile, configures the proportion of 
the grid that is dedicated to empty space (horizontally) between adjacent 
panels. 

The final value `$structure-panel-width` should likely not be set explicitly, as
it is defined as the proportion of the width taken up by a single panel amongst 
the total 100% with the exception of the width taken up by the gutters.

Panels have the following base behavior:

* Below `$breakpoint-small`, all panels are full-width within their containing
element.
* Above `$breakpoint-small`, they are arranged in a fluid grid contingent on the
size of their containing element. As `.row` does not itself specify a width,
this is often a proportion of `.container` if the `.row` that they reside within
is child of a `.container` element.

### Example

```html
<div class="container">
    <div class="row">
        <div class="panel-6">1/2 width</div>
        <div class="panel-4">1/3 width</div>
        <div class="panel-2">1/6 width</div>
    </div>
    <div class="row">
        <div class="panel-12">Full width</div>
    </div>
</div>
```

## Responsive Considerations

While the `.container` provides a fixed max width (if configured as such), the 
container will scale to the max width of the viewport for any viewport widths 
smaller than this fixed max width, while providing a consistent gutter in all
such cases.

The `.panel-X` classes are a proportional grid, but when the viewport is less
than `$breakpoint-small`, all panel classes will arrange themselves as full 
width elements extending down the page.

## Compatibility Considerations

The Bootstrap framework already provides the classes `.container` and `.row`.
To avoid a namespace conflict, the Bootstrap adapter defines `.container` as
`.container:not(.bootstrap)` and `.row` as `.row:not(.bootstrap)`. This mean 
that, if one seeks to use the Bootstrap fixed-width container and row classes
overridden by WebBlocks, it is possible as follows:

```html
<div class="container bootstrap">
    <div class="row bootstrap">
        <div class="span12">Fixed width, full spanning element</div>
    </div>
</div>
```

It is worth note, however, that whatever value `$structure-panels` is set to,
that will be propagated into Bootstrap as the number of `spanX` elements 
generated. This means that, if using the Bootstrap-native `.spanX` classes,
the number `X` will go from 1 to `$structure-panels`. This applies as well for
span elements contained within `.row.bootstrap`, even though in this case they
will behave as the original, fixed-width span elements under `.row` in 
Bootstrap.