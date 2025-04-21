---
layout: post
title:  "Testable WinForms Applications (MVP pattern)"
date:   2025-03-03
tags: [csharp,designpatterns,mvp,winforms]
series: Design Patterns

---
Today, WinForms apps mainly belong to legacy code because of increasing popularity of WPF. And when one team decides about the development stack for the brand new desktop application, they mainly vote for WPF. On the other hand, there are demands for WinForms applications when it is needed to upgrade existing software which is tightly coupled with WinForms, demands for higher performance, etc.

## Different Approaches

There are many opinions on the subject about testable WinForms apps. Some claim that it's not possible to test WinForms apps because there is a lot of dependency between user events and business logic.

  

Standard Windows Form contains Designer.cs partial class and a partial class which contains event handlers for user actions. So, if we follow this same principle and try to implement app logic in event handlers, we can conclude that it would be very hard to write tests for this kind of application. But, if we look at this problem from some distance, we can conclude that every application with user interface could be represented as an interaction between three main components: Data, User Interface and Business Logic.

  

This is the basic idea of MV\* patterns. If we succeed to segregate these three components, then our application is on a good way to be testable. I made a simple WinForms app using MVP pattern. The entire project could be fetched from [github](https://github.com/mirnes-mrkaljevic/testable-winforms-app).

  

In this post, I would just use code snippets to give a general idea of how the code looks like.

  

## But First, Few Words About MVP Pattern

MVP stands for Model-View-Presenter. Model is a component that contains data. It is just a data holder for our forms. View represents the User Interface. It contains design description of our form. Presenter is a component that does most of the job in our WinForms app. It is subscribed to view events and those events are results of user interaction with our form (clicks on buttons, text change, selection change, etc.) and OS interaction with our form (load, show, paint, etc.). Presenter needs to handle all these events and, after their processing, make appropriate action on the view.

  

## Code Structure

Let's start with our view component.

  

```csharp
public interface IProductView
{
     event EventHandler ViewLoad;
     event EventHandler<ProductViewModel> AddNewProduct;
     event EventHandler<ProductViewModel> ModifyProduct;
     event EventHandler<int> DeleteProduct;
     event EventHandler<int> ProductSelected;

     void PopulateDataGridView(IList<Product> products);
     void ClearInputControls();
     void ShowMessage(string message);
}

public partial class Products : Form, IProductView
{
    ...
}
```

We created an interface IProductView that defines rules how Presenter and User Interface component will interact. Now, let's take a look at our presenter component.

  

```csharp
public class ProductPresenter
{
    private IProductView view;
    private IProductDataAccess dataAccesService;
    
    public ProductPresenter(IProductView view, IProductDataAccess dataAccesService)
    {
        this.view = view;
        this.dataAccesService = dataAccesService;
        SubsribeToViewEvents();
    }

    private void SubsribeToViewEvents()
    {
        view.ViewLoad += View_Load;
        view.AddNewProduct += View_AddNewProduct;
        view.ProductSelected += View_ProductSelected;
        view.ModifyProduct += View_ModifyProduct;
        view.DeleteProduct += View_DeleteProduct;
    }
    ...
}
```

Our presenter takes IProductView interface in its constructor. By this way, our view (form) can easily be replaced with another view which implements the same interface. Also, when it comes to mocking, we can easily inject this dependency through constructor. Another component is IProductDataAccess which represents database interface.

  

```csharp
public interface IProductDataAccess
{
    IList<Product> GetAllProducts();
    Product GetProduct(int id);
    bool AddProduct(Product product);
    bool DeleteProduct(int productId);
    bool EditProduct(int productId, Product product);

    string ErrorMessage { get; }
}
  

```

## One Test Case Example

This test case shows how we can easily mock external dependencies in Presenter component.

  

  

```csharp
 [Test]
 public void ExpectToCallAddProductOnAppropriateEventReceived()
 {
     IProductView view = Substitute.For<IProductView>();
     IProductDataAccess dataAccess = Substitute.For<IProductDataAccess>();
     ProductPresenter presenter = new ProductPresenter(view, dataAccess);
           
     ProductViewModel viewModel = new ProductViewModel()
     {
         NameText = "Test",
         PriceText = "2"
     };
           
     view.AddNewProduct += 
          Raise.Event<EventHandler<ProductViewModel>>(view, viewModel);
     dataAccess.Received().AddProduct(Arg.Is<Product>
                           (x=>x.Price == 2 && x.Name == "Test"));
 }
  

```

## Conclusion

This is just a simple example of how powerful an architecture that starts with an abstraction can be. It is always better to start with some components on the higher level and then, defining the interfaces, you define rules how those components will interact with each other.