# extension cicool documentation

for now provides various functions for such purposes

### make extension

> extension structure for your new extension

#### A typical top-level directory layout

    . myextension
    ├── config
    ├── libraries
    ├── ext.json 
    ├── myextension.php    
    └── README.md


on ext.json 

```json
{
    "name" : "my extension",           
    "description" : "my extension",
    "type" : "page",                  
        "version" : "1.0",                
        "author" : "cicool",             
        "regid" : "myextension",          
        "routes" : [
        "administrator/page/(:any)" 
    ],
    "loader" : [
        "myextension.php" 
    ]
}
```

?> `type` for extension like page, crud, rest for groupping

?> `regid` must same like your extension folder name and must unique

?> `routes` is optional extension will be run on specific routes you can define multiple routes

?> `register` file for first loader on your extension you can define multiple

### load library 
```php
app()->load->library('cc_html');
```
?> you can load library from `application/libraries` or `myextension/libraries`

### register script file
```php
app()->cc_html->registerScriptFile('alert("hello")', "script"); 
```
you can fill parameter 2 to file if you load javascript resorce from url 

```php
app()->cc_html->registerScriptFileBottom('http://example.com/js/script.js', "file"); 
```

### register css file
```php
$base_url_extension = url_extension(basename(__DIR__)); 
app()->cc_html->registerCssFile($base_url_extension . 'asset/css/main.css'); 
```

### register menu
```php

cicool()->addMenu(cicool()::ADMIN_SIDEBAR_MENU, [
    'label'         => 'My Extension',
    'type'          => 'menu',
    'icon_color'    => '',
    'link'          => ('unique-url-myextension'),
    'icon'          => 'fa fa-black-tie',
    'menu_type_id'  => cicool()::TYPE_MENU
])->addSubMenu([
        'label'         => 'list invoices',
        'type'          => 'menu',
        'icon_color'    => '',
        'link'          => ('invoice'),
        'icon'          => 'fa fa-user',
    ]);

```

`menu_type_id` can filled `cicool()::TYPE_MENU` or `cicool()::TYPE_LABEL`


### option

#### add option
```php
cicool()->addOption($option_name, $option_value)
```
return `boolean`

#### get option
```php
cicool()->getOption($option_name, $default)
```
return option value if not exist retur default parameter

#### set option
```php
cicool()->setOption($option_name, $option_value)
```
return `boolean` if not exist option will be insert new data 

#### delete option
```php
cicool()->deleteOption($option_name)
```
return boolean 

#### option exists
```php
cicool()->optionExists($option_name)
```
if not exist return is `false`, if exists return option value

### event

#### event listen 
```php
cicool()->eventListen($eventName = null, $params = [])
```

#### on event 
```php
cicool()->onEvent($eventName = null, $action = null)
```
action can e filled like `closure` spesific class method `ClassName::method`

#### get event 
```php
cicool()->getEvent($eventName = null)
```
return `array event registred`


### filter
by filter you can modify variable before execute

#### add filter 
````php
cicool()->addFilter($filterName = null, $action = null)
````

### frontend layout

#### get header
````php
cicool()->getHeader()
````

#### get footer
````php
cicool()->getFooter()
````

#### get navigation
````php
cicool()->getNavigation()
````

### routing

#### on route
this url is containst route 
````php
cicool()->onRoute($route = null, $method = 'get', $action)
````

?> action can filled `closure` or `function string`

example 
````php
cicool()->onRoute('invoice/edit/', $method = 'get', function(){
    //do action here
    });
````

````php
function editInvoice() {
    //do action here
}

cicool()->onRoute('invoice/edit/', $method = 'get', 'editInvoice');
````

#### this route
this url is containst route 
````php
cicool()->thisRoute($route = null, $method = 'get');
````

return `boolean`


### page

#### register page
````php
cicool()->registerPage($pageName, $action = null);
````

example : 

````php
class Invoice Extends Cc_Extension
{
    public function addInvoice($app, $ccExtension)
    {
        $app->render('backend/standart/blank_layouted', [
            'content' => $app->load->view($ccExtension->getExtName().'/invoice_list', [], true)
        ]);
    }
}

cicool()->registerPage('my-url-invoice', function($app) use ($ccExtension, $invoice){
    $invoice->addInvoice($app, $ccExtension);
});

````

?> the `invoice_list` can found on dir your-extension/views/invoice_list.php

#### register class page
````php
cicool()->registerClassPage($class);
````


### tab setting
tab setting you can found on page `administrator/setting`

### create new tab setting 

````php

cicool()->addTabSetting([
    'id' => 'id',
    'label' => 'Builder',
    'icon' => 'fa fa-code',
])->addTabContent([
    'content' => ' 
    <div class="col-md-6">
    
        <div class="col-sm-12">
            <label>Nama <span class="required">*</span></label>
            <input type="text" class="form-control" name="hello_test" id="hello_test" value="'. get_option('hello_test').'">
            <small class="info help-block">The site name.</small>
        </div>

    </div>'

])
->settingBeforeSave(function($form){
    $form->set_rules('hello_test', 'hello_test', 'required');
})
->settingOnSave(function($ci){
    set_option('hello_test', $ci->input->post('hello_test'));
});


````

or you can load view by change value of array content 

````php
$base_url_extension = (basename(__DIR__)); 
app()->load->view($base_url_extension . '/my-setting', [], true);
````