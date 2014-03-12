---
layout: post
title: "Box Models"
date: 2012-06-05 15:30
comments: true
categories: [HTML and CSS Design Patterns]
---

<div><h2>Box Models</h2>

<h3>Problem</h3>

<p>   You want to style the box of an element.</p>

<h3>Solution</h3>

<ul>
<li>The Box Model design pattern is built into CSS. This model defines the relationship between
the following properties: display, width, height, padding, border, margin, background,
overflow, and visibility.</li>
<li><strong>width</strong> normally sets the width of an element’s inner box.</li>
<li><strong>height</strong> normally sets the height of an element’s inner box.</li>
<li><strong>padding</strong> sets the size of the padding surrounding the inner box. The padding is transparent
to the element’s background.</li>
<li><strong>border</strong> sets the size, pattern, and color of the border surrounding the padding.</li>
<li><strong>margin</strong> sets the size of the margin surrounding the border. The margin is transparent to the
background of the element’s parent. The outside of the margin is the element’s outer box.</li>
<li><strong>background</strong> assigns the padding area inside the box to a background color and/or image.</li>
<li><strong>overflow</strong> determines what happens when an element’s content is larger than its inner box.<br>
The default is to show the overflowing content.</li>
<li><strong>visibility</strong> can make the element visible or hidden.

<h3>Pattern</h3>

<pre><code>      SELECTOR { display:CONSTANT;
      overflow:VALUE;
      visibility:VALUE;
      width:+VALUE;
      height:+VALUE;
      padding:+VALUE;
      border:+WIDTH STYLE COLOR;
      margin:±VALUE;
      background:VALUES; }  
</code></pre>

<h3>Location</h3>

This design pattern applies to all elements.

<h3>Example</h3>

The example contains additional HTML markup and CSS rules that are not shown. This
extra code renders a label over each part of the box and draws the outer box and inner box
borders.

<h3>Notes</h3>

CSS defines six main types of boxes: inline, inline-block, block, table, absolute, and floated.
The type of box is determined by the combination of the following properties: display,
position, and float. Box Model properties work differently and produce different layouts
depending on the type of box. Certain types of boxes have additional functionality provided
by additional properties, such as line-height, border-collapse, and table-layout.

<h3>Related to</h3>

All Box Model design patterns

<h2>Box Model Extents</h2></li>
<li><strong>Width</strong> contrasts how width can size, shrinkwrap, or stretch each type of box.</li>
<li><strong>Height</strong> contrasts how height can size, shrinkwrap, or stretch each type of box.</li>
<li><strong>Sized</strong> shows how to set the height or width of an element. An element is sized
when you manually assign it a height and/or a width. For example, you can use
height:50% to size an element’s height to 50% of the height of its container.</li>
<li><strong>Shrinkwrapped</strong> shows how to shrink the width or height of an element to the size
of its content. For example, height:auto causes the height of a static block
element to expand automatically to fit the total height of its lines, and width:auto
causes the width of an absolute element to shrink to fit to the width of its widest
line.</li>
<li><strong>Stretched</strong> shows how to stretch the width or height of an element to the sides of
its container. For example, width:auto causes the width of a static block element
to expand automatically to fit the width of its container. For example, top:0,
bottom:0, and height:auto cause an absolute element to expand automatically to
fit the height of its container. A stretched element’s left side aligns to the left side
of its container, and its right side aligns to the right side of the container. Similarly,
its top and bottom sides align to the top and bottom sides of its container.

<h2>Box Model Properties</h2></li>
<li><strong>Margin</strong> contrasts how margins work differently for different types of boxes. It
shows how margins change the position of an element in relation to its container
and siblings.</li>
<li><strong>Border</strong> contrasts how borders work differently for different types of boxes. It
shows how borders change the position of an element in ways similar to margins,
and in some ways different from margins.</li>
<li><strong>Padding</strong> contrasts how padding works differently for different types of boxes. It
shows how padding works almost identically to borders and margins.</li>
<li><strong>Background</strong> shows how to assign a color to the background of an element. It also
shows how to use a tiled image for the background. You can tile the image across
and down, across only, or down only, or show the image only once. You can
position the image at a specific location in the background. You can also direct
whether the image scrolls with the content or remains in a fixed location.</li>
<li><strong>Overflow</strong> shows how to hide overflowing content, display it, or display scrollbars.</li>
<li><strong>Visibility</strong> shows how to hide an element while leaving a placeholder for it in the
flow.</li>
<li><strong>Page Break</strong> shows how to insert a page break into your document before</li>
</ul></div>