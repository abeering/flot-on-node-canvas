# flot-on-node-canvas

## jquery.flot.js 0.7 hacked to operate within nodejs within jsdom and node-canvas libraries. 

I wish [flot](https://github.com/flot/flot) hadn't *just* moved to github otherwise I'd have forked.  Expect an update now that flot has released 0.8. 

I spent at least a day getting flot to work within [jsdom](https://github.com/tmpvar/jsdom) and [node-canvas](https://github.com/LearnBoost/node-canvas), particularly difficult to get working were the legends.  Some hacks floating around out there get the graph to display correctly but the legends are still broken, legends should display correctly here.

## Usage 

There are some necessary changes for use in node-canvas, a quick example: 

```js

    require('jsdom').env({
        html: '<html><body></body></html>', // URL or markup required
        scripts: [
            'jquery-1.6.4.min.js',
            // Flot 0.7 patched to support node-canvas
            'jquery.flot.js'
        ],
        done: function (errors, window)
        {
            if (errors) {
                console.log( errors );
            }

            // no `window` in node
            var $ = window.$, jQuery = window.jQuery;

            // differences from typical flot usage
            // jQuery (loaded via jsdom) can't determine element dimensions, so:
            // width and height options are required
            // we can just use a stub jQuery object
            var $placeholder = $('div');
            $placeholder.css('width',450);
            $placeholder.css('height',200);
            $placeholder.css('font-size','13px');

            // call Flot as usual
            // Flot data format and options are unchanged
            var $plot = $.plot( $placeholder, data, options )

```

## What's the point? 

Use node-canvas to serve up dynamic chart *images*!

```js

            // get the node-canvas instance
            var nodeCanvas = $plot.getCanvas();

            // write the file
            res.writeHead(200, { 'Content-Type': 'image/png' } );
            nodeCanvas.toBuffer(function (error, buffer) {
                if (error) throw error;
                res.end( buffer );
            });


```

## TODO

Fork from flot 0.8 and update 
