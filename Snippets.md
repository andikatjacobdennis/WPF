1. **Basic UI Components**
    - **Creating a DataGrid**
     - Example:
       ```xaml
        <DataGrid ItemsSource="{Binding People}" 
                  AutoGenerateColumns="False" 
                  IsReadOnly="False" 
                  CanUserAddRows="True" 
                  CanUserDeleteRows="True" 
                  SelectionMode="Single" 
                  AlternatingRowBackground="LightGray" 
                  RowBackground="White" 
                  RowHeaderWidth="30" 
                  ColumnHeaderHeight="40" 
                  GridLinesVisibility="Both" 
                  Sorting="True">
            <DataGrid.Columns>
                <DataGridTextColumn Header="Name" Binding="{Binding Name}" Width="*"/>
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
   - **MVVM Pattern Setup**
     - Example: 
       ```csharp
        using System.ComponentModel;
        
        public class MyViewModel : INotifyPropertyChanged
        {
            private string _buttonText;
        
            public string ButtonText
            {
                get => _buttonText;
                set
                {
                    if (_buttonText != value)
                    {
                        _buttonText = value;
                        OnPropertyChanged(nameof(ButtonText));
                    }
                }
            }
        
            public MyViewModel()
            {
                ButtonText = "Click Me"; // Default text
            }
        
            public event PropertyChangedEventHandler PropertyChanged;
        
            protected virtual void OnPropertyChanged(string propertyName)
            {
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
            }
        }
       ```
  
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
       public class BoolToVisibilityConverter : IValueConverter { ... }
       ```
   - **Multi Value Converters**
     - Example: 
       ```csharp
       public class BoolToVisibilityConverter : IValueConverter { ... }
       ```
   - **Using Attached Properties**
     - Example: 
       ```csharp
       public static readonly DependencyProperty IsMouseOverProperty = ...;
       ```
   - **Implementing a Custom Behavior**
     - Example: 
       ```csharp
       public class ClickBehavior : Behavior<Button> { ... }
       ```

5. **Animations and Dynamic UI**
   - **Using Animations**
     - Example: 
       ```xaml
       <Button Name="MyButton" Content="Animate Me">
           <Button.Triggers>
               <EventTrigger RoutedEvent="Button.MouseEnter">
                   <BeginStoryboard>
                       <Storyboard>
                           <DoubleAnimation Storyboard.TargetName="MyButton" Storyboard.TargetProperty="Opacity" From="1" To="0.5" Duration="0:0:0.5" AutoReverse="True"/>
                       </Storyboard>
                   </BeginStoryboard>
               </EventTrigger>
           </Button.Triggers>
       </Button>
       ```
   - **Dynamic Resource Changes**
     - Example: 
       ```csharp
       Application.Current.Resources["PrimaryColor"] = new SolidColorBrush(Colors.Red);
       ```
   - **Using Background Workers for Asynchronous Operations**
     - Example: 
       ```csharp
       BackgroundWorker worker = new BackgroundWorker();
       worker.DoWork += (sender, e) => { /* long-running task */ };
       worker.RunWorkerCompleted += (sender, e) => { /* update UI */ };
       worker.RunWorkerAsync();
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
