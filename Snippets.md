1. **Basic UI Components**
    - **Creating a DataGrid**
```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Price { get; set; }
}
```

```csharp
using System.Collections.ObjectModel;
using System.ComponentModel;
using System.Windows.Data;

public class ProductViewModel : INotifyPropertyChanged
{
    private ICollectionView _productCollectionView;
    private string _filterText;
    
    public ObservableCollection<Product> Products { get; set; }

    public ICollectionView ProductCollectionView
    {
        get { return _productCollectionView; }
        set
        {
            _productCollectionView = value;
            OnPropertyChanged(nameof(ProductCollectionView));
        }
    }

    public string FilterText
    {
        get { return _filterText; }
        set
        {
            _filterText = value;
            OnPropertyChanged(nameof(FilterText));
            ProductCollectionView.Refresh();
        }
    }

    public ProductViewModel()
    {
        // Initialize the Products collection
        Products = new ObservableCollection<Product>
        {
            new Product { Id = 1, Name = "Product A", Price = 12.50M },
            new Product { Id = 2, Name = "Product B", Price = 25.00M },
            new Product { Id = 3, Name = "Product C", Price = 30.00M }
        };

        // Initialize CollectionView and add a filter
        ProductCollectionView = CollectionViewSource.GetDefaultView(Products);
        ProductCollectionView.Filter = FilterProducts;
    }

    private bool FilterProducts(object obj)
    {
        if (obj is Product product)
        {
            return string.IsNullOrEmpty(FilterText) || 
                   product.Name.Contains(FilterText, StringComparison.OrdinalIgnoreCase);
        }
        return false;
    }

    public event PropertyChangedEventHandler PropertyChanged;
    protected virtual void OnPropertyChanged(string propertyName)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

```xml
<!-- Filter TextBox -->
<TextBox Width="200" Margin="10"
         Text="{Binding FilterText, UpdateSourceTrigger=PropertyChanged}" 
         PlaceholderText="Filter by Name"/>

<!-- DataGrid -->
<DataGrid ItemsSource="{Binding ProductCollectionView}" AutoGenerateColumns="False" 
          Margin="10" CanUserAddRows="False" IsReadOnly="True">
    <DataGrid.Columns>
        <DataGridTextColumn Header="ID" Binding="{Binding Id}" Width="50"/>
        <DataGridTextColumn Header="Name" Binding="{Binding Name}" Width="200"/>
        <DataGridTextColumn Header="Price" Binding="{Binding Price, StringFormat=C}" Width="100"/>
    </DataGrid.Columns>
</DataGrid>
```

2. **Styling and Templates**
   - **Using Styles and Triggers**
     - Example:
    
       ```xaml
        <Style TargetType="Button">
            <Setter Property="BorderBrush" Value="DarkGray"/>
            <Setter Property="BorderThickness" Value="1"/>
            <Setter Property="CornerRadius" Value="5"/>
            <Setter Property="Cursor" Value="Hand"/>
            
            <Style.Triggers>
                <Trigger Property="IsMouseOver" Value="True">
                    <Setter Property="Background" Value="Blue"/>
                    <Setter Property="ScaleTransform">
                        <Setter.Value>
                            <ScaleTransform ScaleX="1.1" ScaleY="1.1"/>
                        </Setter.Value>
                    </Setter>
                </Trigger>
                <Trigger Property="IsPressed" Value="True">
                    <Setter Property="Background" Value="DarkBlue"/>
                </Trigger>
                <Trigger Property="IsEnabled" Value="False">
                    <Setter Property="Background" Value="LightGray"/>
                    <Setter Property="Opacity" Value="0.5"/>
                </Trigger>

                <!-- DataTrigger: Change opacity when the button is disabled -->
                <DataTrigger Binding="{Binding IsEnabled}" Value="False">
                    <Setter Property="Opacity" Value="0.5"/>
                </DataTrigger>
        
                <!-- MultiDataTrigger: Change background based on multiple conditions -->
                <MultiDataTrigger>
                    <MultiDataTrigger.Conditions>
                        <Condition Binding="{Binding IsMouseOver}" Value="True"/>
                        <Condition Binding="{Binding IsEnabled}" Value="True"/>
                    </MultiDataTrigger.Conditions>
                    <Setter Property="Background" Value="LightGreen"/>
                    <Setter Property="Foreground" Value="Black"/>
                </MultiDataTrigger>
                    </Style.Triggers>
           </Style>

       ```
       
   - **Control Templates**
     - Example: 
       ```xaml
       
        <Window.Resources>
            <ControlTemplate x:Key="CustomButtonTemplate" TargetType="Button">
                <Border Background="{TemplateBinding Background}" 
                        BorderBrush="{TemplateBinding BorderBrush}" 
                        BorderThickness="{TemplateBinding BorderThickness}" 
                        CornerRadius="5">
                    <ContentPresenter 
                        HorizontalAlignment="{TemplateBinding HorizontalContentAlignment}" 
                        VerticalAlignment="{TemplateBinding VerticalContentAlignment"/>
                </Border>
            </ControlTemplate>
    
            <Style TargetType="Button">
                <Setter Property="Template" Value="{StaticResource CustomButtonTemplate}"/>
                <Setter Property="Background" Value="LightGray"/>
            </Style>
        </Window.Resources>

       ```
   - **Resource Dictionaries for Themes**
     - Example: 
       ```xaml
        <!-- MainResourceDictionary.xaml -->
        <ResourceDictionary xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
                            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
            <ResourceDictionary.MergedDictionaries>
                <ResourceDictionary Source="Themes/LightTheme.xaml"/>
                <!-- You can add more dictionaries here if needed -->
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>

        <Window.Resources>
            <ResourceDictionary Source="MainResourceDictionary.xaml"/>
        </Window.Resources>
       ```

3. **Data Binding and MVVM** 
   - **Using Commands with Parameters**
     - Example: 
       ```csharp
        using System;
        using System.Windows.Input;
        
        public class RelayCommand : ICommand
        {
            private readonly Action<object> _execute;
            private readonly Predicate<object> _canExecute;
        
            public RelayCommand(Action<object> execute, Predicate<object> canExecute = null)
            {
                _execute = execute ?? throw new ArgumentNullException(nameof(execute));
                _canExecute = canExecute;
            }
        
            public event EventHandler CanExecuteChanged
            {
                add => CommandManager.RequerySuggested += value;
                remove => CommandManager.RequerySuggested -= value;
            }
        
            public bool CanExecute(object parameter) => _canExecute == null || _canExecute(parameter);
        
            public void Execute(object parameter) => _execute(parameter);
        }
       ```

       ```csharp
        using System.Windows.Input;
        
        public class MyViewModel
        {        
            public ICommand ButtonCommand { get; }
        
            public MyViewModel()
            {
                ButtonCommand = new RelayCommand(ExecuteButtonCommand);
            }
        
            private void ExecuteButtonCommand(object parameter)
            {
                if (parameter is string message)
                {
                    // Use the parameter (e.g., show a message box)
                    System.Windows.MessageBox.Show(message);
                }
            }
        }

       ```

4. **Advanced Features**
   - **Single Value Converters**
     - Example:
       
       ```csharp

            using System;
            using System.Globalization;
            using System.Windows.Data;
            
            public class BooleanToStringConverter : IValueConverter
            {
                public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
                {
                    if (value is bool booleanValue)
                    {
                        return booleanValue ? "Yes" : "No";
                    }
                    return "No"; // Default value
                }
            
                public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
                {
                    if (value is string stringValue)
                    {
                        return stringValue.Equals("Yes", StringComparison.OrdinalIgnoreCase);
                    }
                    return false; // Default value
                }
            }

       ```
            
        ```xml
        
        <Window x:Class="MyNamespace.MainWindow">
            <Window.Resources>
                <local:BooleanToStringConverter x:Key="BooleanToStringConverter"/>
            </Window.Resources>
            
            <Grid>
                <StackPanel>
                    <TextBlock Text="{Binding IsChecked, ElementName=MyCheckBox, Converter={StaticResource BooleanToStringConverter}}"
                               Margin="10"/>
                </StackPanel>
            </Grid>
        </Window>
        
        ```
       - **Multi Value Converters**
         - Example: 
           ```csharp
            using System;
            using System.Globalization;
            using System.Windows.Data;
            
            public class CombineValuesConverter : IMultiValueConverter
            {
                public object Convert(object[] values, Type targetType, object parameter, CultureInfo culture)
                {
                    if (values.Length >= 2 &&
                        values[0] is string str1 &&
                        values[1] is string str2)
                    {
                        return $"{str1} {str2}"; // Combine the two strings
                    }
                    return string.Empty; // Default value
                }
            
                public object[] ConvertBack(object value, Type[] targetTypes, object parameter, CultureInfo culture)
                {
                    var parts = value.ToString().Split(' ', 2);
                    return new object[] { parts[0], parts.Length > 1 ? parts[1] : string.Empty };
                }
            }
           ```

       - **Using Attached Properties**
         - Example: 
           ```csharp

            using System.Windows;
            using System.Windows.Controls;
            
            public static class ToolTipAttachedProperty
            {
                public static readonly DependencyProperty ToolTipTextProperty =
                    DependencyProperty.RegisterAttached(
                        "ToolTipText",
                        typeof(string),
                        typeof(ToolTipAttachedProperty),
                        new PropertyMetadata(string.Empty, OnToolTipTextChanged));
            
                public static string GetToolTipText(DependencyObject obj)
                {
                    return (string)obj.GetValue(ToolTipTextProperty);
                }
            
                public static void SetToolTipText(DependencyObject obj, string value)
                {
                    obj.SetValue(ToolTipTextProperty, value);
                }
            
                private static void OnToolTipTextChanged(DependencyObject d, DependencyPropertyChangedEventArgs e)
                {
                    if (d is Control control)
                    {
                        control.ToolTip = e.NewValue?.ToString();
                    }
                }
            }

           ```
       - **Implementing a Custom Behavior**
         - Example: ClickColorChangeBehavior

        In WPF, custom behaviors allow you to add functionality to existing controls in a reusable way without modifying their code. You can implement behaviors using the `Behavior<T>` class from the                     `System.Windows.Interactivity` namespace (or `Microsoft.Xaml.Behaviors` in newer projects).

        ```csharp
        using System.Windows;
        using System.Windows.Controls;
        using System.Windows.Media;
        using System.Windows.Input;
        using System.Windows.Interactivity;
        
        public class ClickColorChangeBehavior : Behavior<UIElement>
        {
            public Color ClickColor
            {
                get { return (Color)GetValue(ClickColorProperty); }
                set { SetValue(ClickColorProperty, value); }
            }
        
            public static readonly DependencyProperty ClickColorProperty =
                DependencyProperty.Register("ClickColor", typeof(Color), typeof(ClickColorChangeBehavior), new PropertyMetadata(Colors.Transparent));
        
            protected override void OnAttached()
            {
                base.OnAttached();
                AssociatedObject.MouseLeftButtonDown += OnMouseLeftButtonDown;
            }
        
            protected override void OnDetaching()
            {
                base.OnDetaching();
                AssociatedObject.MouseLeftButtonDown -= OnMouseLeftButtonDown;
            }
        
            private void OnMouseLeftButtonDown(object sender, MouseButtonEventArgs e)
            {
                if (AssociatedObject is Control control)
                {
                    control.Background = new SolidColorBrush(ClickColor);
                }
            }
        }
        ```
        
        ```xml
        <Window x:Class="MyNamespace.MainWindow"
                xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
                xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
                xmlns:i="http://schemas.microsoft.com/expression/2010/interactivity"
                xmlns:local="clr-namespace:MyNamespace"
                Title="Custom Behavior Example" Height="300" Width="400">
            <StackPanel Margin="10">
                <Button Content="Click Me" Width="100" Height="30">
                    <i:Interaction.Behaviors>
                        <local:ClickColorChangeBehavior ClickColor="LightCoral"/>
                    </i:Interaction.Behaviors>
                </Button>
                <TextBox Width="200" Height="30" Margin="0,10,0,0">
                    <i:Interaction.Behaviors>
                        <local:ClickColorChangeBehavior ClickColor="LightBlue"/>
                    </i:Interaction.Behaviors>
                </TextBox>
            </StackPanel>
        </Window>
        ```
        
5. **Animations and Dynamic UI**
   - **Using Animations**
     - Example: 
       ```xaml
        <Button Name="MyButton" Content="Animate Me" Width="200" Height="50" Background="LightBlue" Foreground="White" FontSize="16">
            <Button.Triggers>
                <EventTrigger RoutedEvent="Button.MouseEnter">
                    <BeginStoryboard>
                        <Storyboard>
                            <!-- Opacity Animation -->
                            <DoubleAnimation Storyboard.TargetName="MyButton" Storyboard.TargetProperty="Opacity" From="1" To="0.5" Duration="0:0:0.5" AutoReverse="True"/>
                            
                            <!-- Scale Animation -->
                            <DoubleAnimation Storyboard.TargetName="MyButton" Storyboard.TargetProperty="(UIElement.RenderTransform).(ScaleTransform.ScaleX)" From="1" To="1.2" Duration="0:0:0.5" AutoReverse="True"/>
                            <DoubleAnimation Storyboard.TargetName="MyButton" Storyboard.TargetProperty="(UIElement.RenderTransform).(ScaleTransform.ScaleY)" From="1" To="1.2" Duration="0:0:0.5" AutoReverse="True"/>
        
                            <!-- Background Color Animation -->
                            <ColorAnimation Storyboard.TargetName="MyButton" Storyboard.TargetProperty="(Button.Background).(SolidColorBrush.Color)" From="LightBlue" To="Coral" Duration="0:0:0.5" AutoReverse="True"/>
                            
                            <!-- Easing Function for smoother animation -->
                            <DoubleAnimationUsingKeyFrames Storyboard.TargetName="MyButton" Storyboard.TargetProperty="(UIElement.RenderTransform).(ScaleTransform.ScaleX)">
                                <EasingDoubleKeyFrame KeyTime="0:0:0.5" Value="1.2" EasingFunction="{StaticResource QuadraticEase}"/>
                            </DoubleAnimationUsingKeyFrames>
                            <DoubleAnimationUsingKeyFrames Storyboard.TargetName="MyButton" Storyboard.TargetProperty="(UIElement.RenderTransform).(ScaleTransform.ScaleY)">
                                <EasingDoubleKeyFrame KeyTime="0:0:0.5" Value="1.2" EasingFunction="{StaticResource QuadraticEase}"/>
                            </DoubleAnimationUsingKeyFrames>
                        </Storyboard>
                    </BeginStoryboard>
                </EventTrigger>
            </Button.Triggers>
            <Button.RenderTransform>
                <ScaleTransform />
            </Button.RenderTransform>
        </Button>

       ```
   - **Dynamic Resource Changes**
     - Example: 
       ```csharp
            var lightTheme = new ResourceDictionary { Source = new Uri("LightTheme.xaml", UriKind.Relative) };
            Application.Current.Resources.MergedDictionaries.Clear();
            Application.Current.Resources.MergedDictionaries.Add(lightTheme);
        ```
   - **Begin Invoke**
     - Example: 
       ```csharp
            Application.Current.Dispatcher.BeginInvoke(new Action(() =>
            {
                StatusTextBlock.Text = "Background work completed!";
            }));
       ```

6. **Error Handling and Best Practices**
   - **Handling Global Exceptions**
     - Example: 
       ```csharp
       Application.Current.DispatcherUnhandledException += (sender, e) =>
       {
           MessageBox.Show($"An error occurred: {e.Exception.Message}");
           e.Handled = true;
       };
       ```
