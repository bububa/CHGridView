#CHGridView

![A screenshot of CHGridView](http://cameron.io/files/CHGridView-sizedToGrid.png) ![A second screenshot of CHGridView](http://cameron.io/files/CHGridView-centered.png)

(Above is two screenshots of CHGridView with CHImageTileView, one with Photos-style layout, and another with iPhoto-style layout.)

###About:

I want this to be the last grid view I make. Right now, it's basically a UITableView clone with tiles instead of cells, but in the future I want it to be customizable and flexible enough to reproduce paged icon views, handle un-scrollable grids, display 2000 images, show iPad-like photo stacks with pinch-to-open, and anything else that can be represented with a grid.

CHGridView is modeled after UITableView. You initialize CHGridView, set a delegate and data source, then give it tiles. It's designed to be as easy to use as UITableView.

NOTICE: **CHGridView is not production-level software yet. Don't use it in shipping software.**

###Description of classes:

- CHGridView: UIScrollView subclass that handles loading and display of tiles and section titles.
- CHTileView: UIView subclass to draw content, intended to be subclassed for specific use.
- CHImageTileView: CHTileView subclass to easily display images with a border, similar to Apple's Photos application.
- CHSectionHeaderView: UIView subclass to display section headers, can be subclassed and used for custom section titles.
- CHGridLayout: Computes layout and caches it in a big array, making it easy for CHGridView to only display visible tiles and section titles.
- CHGridLayoutTile: Simple object that stores `indexPath` and `rect` for a tile.
- CHGridLayoutSection: Simple object to store section index and its Y co-ordinate position.

###Usage:

Exactly like UITableView. Just implement the two required data source methods: `numberOfTilesInSection` and `tileForIndexPath`. CHGridView assumes there is at least one section. The method `tileForIndexPath` works very much like UITableView; CHGridView reuses tiles like UITableView reuses cells. Call `dequeueReusableTileWithIdentifier` to get a reusable tile, if it's `nil`, `init` and `autorelease` a new tile and return it.

There's two basic styles to use in GHGridView, one that resembles the Photos application, and one that mimics iPhoto and the iPad photo grid. The property that controls it is called `centerTilesInGrid`. Set it to `YES` for the iPhoto  style. If you want the iPhoto style, you need to make sure `rowHeight` is higher than your tallest image.

Row height, tiles per line, padding, section title height and shadow are all properties of CHGridView. These are not meant to change often like the data source and delegate methods. However, if you do change them, make sure to call `reloadData` to recalculate the layout.

Shadows are drawn in Core Graphics and add to your padding. Adjust shadow properties and padding until a desirable result is achieved.

Orientation changes and resizing are supported. Section titles and tiles are set to the correct autoresizing mask, but you should call `reloadData` after you resize CHGridView for optimal re-layout, same goes for re-orientation.

If you disable scrolling with `setScrollingEnabled`, you can probably use this as a un-scrollable grid view, but I haven't tested it.

###Behind the scenes:

CHGridView only loads visible tiles and section titles, plus four rows above and beneath (on the Original iPhone, iPhone 3G or first-generation iPod Touch, it only loads two rows). There's only about 30 or 50 tiles loaded at any single time.

CHGridView, like most scalable computer interfaces, makes trade-offs. It's currently optimized to for low memory usage, but requires more processor cyles. Performance is roughly the same if you use 3000 tiles or 200 tiles. Optimally CHGridView should be smart enough to switch this trade-off at some point (if < 400 tiles, for instance).

- CHTileView shadows are not transparent, they are rendered onto the same background color as CHGridView. It's possible to change it if you long for the scrolling performance of Android or Palm webOS.
- Section headers are only transparent when they need to be, otherwise they are opaque. If you subclass CHSectionHeaderView, you'll need to check `[self isOpaque]` to compensate for transparency if needed.

###Roadmap (roughly in order):

- Match or exceed speed and performance found in iPhone's Photos app grid view
- More UITableView cloning, like scrolling to a tile at a specific indexPath
- Footer labels (with dynamic height) and footer views
- Tile labels
- Flexible per-line setting (maybe a range?)
- Horizontal scrolling and paging support
- Add/remove tile animation
- Multiple selection support
- Nice re-ordering

###Performance:

I've tested CHGridView informally with a test application on both my iPhones. For my data source, I used 31 images to populate 2,976 tiles separated into 96 sections. They were exported from iPhoto as PNGs with a maximum width of 160 pixels, then  were drawn centered and cropped in CHImageTileView. Scrolling performance is not as good as Apple's Photos grid view, especially on my original iPhone.

- Original iPhone: about 18 - 35 fps.
- iPhone 3G3: about 30 - 50 fps.

Admittedly, performance could be better. If you see something that could be improved, send an email to [me@cameron.io](mailto:me@cameron.io).
