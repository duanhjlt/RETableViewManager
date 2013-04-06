# RETableViewManager

__Data driven content manager for UITableView.__

RETableViewManager allows to manage content of `UITableView` with ease, both forms and lists.
In its core RETableViewManager supports reusable cells based on corresponding data object class.

The general idea is to allow developers use their own `UITableView` and `UITableViewController` instances,
providing a layer that synchronizes data and cell appereance.

### _This is still in the early stages of development and it's highly not recommended to use it in production apps._

![Screenshot of RETableViewManager](https://github.com/romaonthego/RETableViewManager/raw/master/Screenshot.png "RETableViewManager Screenshot")

## Requirements
* Xcode 4.5 or higher
* Apple LLVM compiler
* iOS 5.0 or higher
* ARC

## Demo

Build and run the `RETableViewManagerExample` project in Xcode to see `RETableViewManager` in action.

## API Quickstart

<table>
  <tr><th colspan="2" style="text-align:center;">Key Classes</th></tr>
  <tr>
    <td>RETableViewManager</td>
    <td>Main manager class. Each manager has multiple <tt>RETableViewSection</tt> sections.</td>
  </tr>
  <tr>
    <td>RETableViewSection</td>
    <td>Represents sections in RETableViewManager, each section has multiple <tt>RETableViewItem</tt>.</td>
  </tr>
  <tr>
    <td>RETableViewItem</td>
    <td>RETableViewItem is the root class of most <tt>RETableViewManager</tt> item hierarchies.<br />
    Through <tt>RETableViewItem</tt>, items inherit a basic interface that communicates with <tt>RETableViewCell</tt> and <tt>RETableViewManager</tt>.</td>
  </tr>
  <tr>
    <td>RETableViewCell</td>
    <td>The <tt>RETableViewCell</tt> class defines the attributes and behavior of the cells that appear in <tt>UITableView</tt> objects.
     You should subclass <tt>RETableViewCell</tt> to obtain cell characteristics and behavior specific to your application's needs.
     By default, <tt>RETableViewCell</tt> is being mapped with <tt>RETableViewItem</tt>.</td>
  </tr>
  <tr><th colspan="2" style="text-align:center;">Styling</th></tr>
  <tr>
    <td>RETableViewCellStyle</td>
    <td>Provides style for <tt>RETableViewCell</tt> subclasses. You can define such properties as
    <tt>textFieldFont</tt>, <tt>cellHeight</tt>, <tt>backgroundImage</tt> and more.</td>
  </tr>
  <tr><th colspan="2" style="text-align:center;">Helper Controllers</th></tr>
  <tr>
    <td>RETableViewOptionsController</td>
    <td>Performs selection based on user input and provides result on completion. Should be used with RERadioItem.
    </td>
  </tr>
</table>

RETableViewManager includes a number of built-in items and cells that were mapped with each other so you don't have to.

<table>
  <tr><th colspan="3" style="text-align:center;">Built-in Items and Cells</th></tr>
  <tr>
    <th>Item Class</th>
    <th>Cell Class</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>RETextItem</td>
    <td>RETableViewTextCell</td>
    <td>Provides convenience for user text input. You can set a bunch of properties through <tt>RETextItem</tt> that you would
    normally find in <tt>UITextField</tt>.</td>
  </tr>
  <tr>
    <td>RENumberItem</td>
    <td>RETableViewNumberCell</td>
    <td>Provides convenience for user number input using <a href="https://github.com/romaonthego/REFormattedNumberField">REFormattedNumberField</a>.</td>
  </tr>
  <tr>
    <td>REBoolItem</td>
    <td>RETableViewBoolCell</td>
    <td>Provides convenience for user boolean input using <tt>UISwitch</tt>.</td>
  </tr>
  <tr>
    <td>RERadioItem</td>
    <td>RETableViewCell</td>
    <td>Provides convenience for selecting options using <tt>RETableViewOptionsController</tt>.</td>
  </tr>
  <tr>
    <td>REFloatItem</td>
    <td>RETableViewFloatCell</td>
    <td>Provides convenience for adjusting float values from 0.0 to 1.0.</td>
  </tr>
  <tr>
    <td>REDateTimeItem</td>
    <td>RETableViewDateTimeCell</td>
    <td>Provides convenience for adjusting NSDate object.</td>
  </tr>
  <tr>
    <td>RECreditCardItem</td>
    <td>RETableViewCreditCardCell</td>
    <td>Provides convenience for user credit card input. Allows to enter credit card number, expiration date and security code all in one table cell.</td>
  </tr>
</table>

## Examples

### Simple Example

Subclass UITableViewController, remove all code inside of the implementation section and paste just this
(yes, now it's that easy to manage `UITableView` content):

``` objective-c
- (void)viewDidLoad
{
    [super viewDidLoad];
    // Create the manager and assign it to be the delegate and datasource of your UITableView
    //
    _tableViewManager = [[RETableViewManager alloc] init];
    self.tableView.delegate = _tableViewManager;
    self.tableView.dataSource = _tableViewManager;

    // Add a section
    //
    RETableViewSection *section = [[RETableViewSection alloc] initWithHeaderTitle:@"Test"];
    [_tableViewManager addSection:section];

    // Add a string
    //
    [section addItem:@"Just a simple NSString"];

    // Add a basic cell with disclosure indicator
    //
    [section addItem:[RETableViewItem itemWithTitle:"String cell" accessoryType:UITableViewCellAccessoryDisclosureIndicator actionBlock:^(RETableViewItem *item) {
        NSLog(@"Test: %@", item);
    }]];
}
```

### Creating Sections Example

Section without a title:

``` objective-c
RETableViewSection *section = [RETableViewSection section];
[_tableViewManager addSection:section];
```

Section with a title:

``` objective-c
RETableViewSection *section = [RETableViewSection sectionWithHeaderTitle:@"Header"];
[_tableViewManager addSection:section];
```

Section with a title and a footer:

``` objective-c
RETableViewSection *section = [RETableViewSection sectionWithHeaderTitle:@"Header" footerTitle:@"Footer"];
[_tableViewManager addSection:section];
```

Section with a custom title view:

``` objective-c
RETableViewSection *section = [RETableViewSection sectionWithHeaderView:myCustomSectionHeaderView];
[_tableViewManager addSection:section];
```

### Item to Cell Mapping Example

It's super easy to create custom mappings, for example, this how all `NSString` objects are being matched with `RETableViewCell`:

``` objective-c
[_tableViewManager mapObjectClass:@"NSString" toTableViewCellClass:@"RETableViewCell"];
```

If you take a look at [RETableViewManager Source Code](https://github.com/romaonthego/RETableViewManager/blob/master/RETableViewManager/RETableViewManager.m) you may
find out how default mapping is performed:

``` objective-c
- (void)setDefaultMapping
{
    [self mapObjectClass:@"__NSCFConstantString" toTableViewCellClass:@"RETableViewCell"];
    [self mapObjectClass:@"__NSCFString" toTableViewCellClass:@"RETableViewCell"];
    [self mapObjectClass:@"NSString" toTableViewCellClass:@"RETableViewCell"];
    [self mapObjectClass:@"RETableViewItem" toTableViewCellClass:@"RETableViewCell"];
    [self mapObjectClass:@"RERadioItem" toTableViewCellClass:@"RETableViewCell"];
    [self mapObjectClass:@"REBoolItem" toTableViewCellClass:@"RETableViewBoolCell"];
    [self mapObjectClass:@"RETextItem" toTableViewCellClass:@"RETableViewTextCell"];
    [self mapObjectClass:@"RENumberItem" toTableViewCellClass:@"RETableViewNumberCell"];
    [self mapObjectClass:@"RECreditCardItem" toTableViewCellClass:@"RETableViewCreditCardCell"];
}
```

### Text (UITextField) and Number (REFormattedNumberField) Item Example

``` objective-c
// Create the manager and assign it to be the delegate and datasource of your UITableView
//
_tableViewManager = [[RETableViewManager alloc] init];
self.tableView.delegate = _tableViewManager;
self.tableView.dataSource = _tableViewManager;

// Add a section
//
RETableViewSection *section = [[RETableViewSection alloc] initWithHeaderTitle:@"Test"];
[_tableViewManager addSection:section];

_textItem = [RETextItem itemWithTitle:@"Enter text" value:@""];
[section addItem:_textItem];

_numberItem = [RENumberItem itemWithTitle:@"Enter text" value:@"" placeholder:@"(123) 456-7890" format:@"(XXX) XXX-XXXX"];
[section addItem:_numberItem];
```

You can read `_textItem.value` and `_numberItem.value` later whenever you need them.

### Bool Item (UISwitch) Example

``` objective-c
// Create the manager and assign it to be the delegate and datasource of your UITableView
//
_tableViewManager = [[RETableViewManager alloc] init];
self.tableView.delegate = _tableViewManager;
self.tableView.dataSource = _tableViewManager;

// Add a section
//
RETableViewSection *section = [[RETableViewSection alloc] initWithHeaderTitle:@"Test"];
[_tableViewManager addSection:section];

// Add a bool value cell (using UISwitch)
//
[section addItem:[REBoolItem itemWithTitle:@"Switch test" value:YES actionBlock:^(REBoolItem *item) {
    NSLog(@"Value: %i", item.value);
}]];
```

### Radio (RETableViewOptionsController) Item Example

``` objective-c
// Create the manager and assign it to be the delegate and datasource of your UITableView
//
_tableViewManager = [[RETableViewManager alloc] init];
self.tableView.delegate = _tableViewManager;
self.tableView.dataSource = _tableViewManager;

// Add a section
//
RETableViewSection *section = [[RETableViewSection alloc] initWithHeaderTitle:@"Test"];
[_tableViewManager addSection:section];

// Add radio cell (options)
//
__typeof (&*self) __weak weakSelf = self;
RERadioItem *optionItem = [RERadioItem itemWithTitle:@"Radio" value:@"Option 4" actionBlock:^(RERadioItem *item) {
  [weakSelf.tableView deselectRowAtIndexPath:item.indexPath animated:YES];

  // Generate sample options
  //
  NSMutableArray *options = [[NSMutableArray alloc] init];
  for (NSInteger i = 1; i < 40; i++)
      [options addObject:[NSString stringWithFormat:@"Option %i", i]];

  // Present options controller
  //
  RETableViewOptionsController *optionsController = [[RETableViewOptionsController alloc] initWithItem:item options:options completionHandler:^(RETableViewItem *selectedItem) {
      item.value = selectedItem.title;
      [weakSelf.tableView reloadRowsAtIndexPaths:@[item.indexPath] withRowAnimation:UITableViewRowAnimationNone];
  }];
  [weakSelf.navigationController pushViewController:optionsController animated:YES];
}];
[section addItem:optionItem];
```

### Float Item (UISlider) Example

``` objective-c
// Create the manager and assign it to be the delegate and datasource of your UITableView
//
_tableViewManager = [[RETableViewManager alloc] init];
self.tableView.delegate = _tableViewManager;
self.tableView.dataSource = _tableViewManager;

// Add a section
//
RETableViewSection *section = [[RETableViewSection alloc] initWithHeaderTitle:@"Test"];
[_tableViewManager addSection:section];

// Add a float item
//
[section addItem:[REFloatItem itemWithTitle:@"Float item" value:0.3 actionBlock:^(REFloatItem *item) {
    NSLog(@"Value: %f", item.value);
}]];
```

## Contact

Roman Efimov

- https://github.com/romaonthego
- https://twitter.com/romaonthego
- romefimov@gmail.com

## License

RETableViewManager is available under the MIT license.

Copyright © 2013 Roman Efimov.

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
