# alfreed
[Apparence.io](https://apparence.io) studio flutter management library.
This lib is used for our apps and is open for all. 

This force split business logic / view for your pages. 


## Install
Add alfreed in your pubspec and import it. 
```dart
import 'package:alfreed/alfreed.dart';
```

## Getting Started

### Create a page builder
```dart
var myPageBuilder = AlfreedPageBuilder<MyPresenter, MyModel, ViewInterface>(
  key: ValueKey("presenter"),
  builder: (ctx, presenter, model) {
    return Scaffold(
      appBar: AppBar(title: Text(model.title ?? "")),
      // ... you page widgets are here
    );
  },
  presenterBuilder: (context) => MyPresenter(),
  interfaceBuilder: (context) => ViewInterface(context),
);
```

Every build or defered to let our navigation beeing responsible for building them. (see routing section).

* **builder**: build your flutter page content (widgets...)
* **presenterBuilder**: build your presenter (business logic class)
* **interfaceBuilder**: build your view interface (business logic call this class to interact with our application without knowing flutter). Goal is to hide flutter from our business logic. (***Example: showSnackBar(String message) Without any context in parameters***)
* **key**(***optionnal***): used to get a reference to the presenter

### Create a presenter 
Create a presenter extending ```Presenter``` class. 
This is where you write your business logic. 
There is only and only one presenter instance available in the widget tree. 

>An instance of this will be available in the builder method seen in previous stage (Create a page builder). 

```dart
class MyPresenter extends Presenter<MyModel, ViewInterface> {
    // ... write you business logic methods here
}
```

### Create state class
Basically this will contain everything you want to show on your page. This model is a simple class where you can wrap models ```ValueNotifier``` or ```Stream```.

```dart
class ViewInterface extends AlfreedView {
  ViewInterface(BuildContext context) : super(context: context);

  /// ... all your attributes you want to use in your page

  void showMessage(String message) {
    final snackBar = SnackBar(content: Text(message));
    ScaffoldMessenger.of(context).showSnackBar(snackBar);
  }
}
```

## Routing

You can wrap our builder directly in your app router. 
<br/>Like this:

```dart
Route<dynamic> route(RouteSettings settings) {
  switch(settings.name) {
      case "/":
        return MaterialPageRoute(builder: myPageBuilder.build);
      default:  
        return MaterialPageRoute(builder: errorPage.build);
  }
}
```

## Test

### Get presenter ref
Use ```AlfreedUtils``` to get a reference of your presenter instance. 
```dart
var presenter = AlfreedUtils.getPresenterByKey<MyPresenter, MyModel>(
    tester, ValueKey("presenter"));
```

* The Key must be unique and added to the ```AlfreedPageBuilder``` seen on step (***Create a page builder***)
* The application must be started using a pumpWidget
* the page is correctly build

### Mock presenter
Prefer using a real presenter but in some case this helps. 

> ***Doc incoming***
