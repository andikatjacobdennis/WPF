### Controls and Layout in WPF

Windows Presentation Foundation (WPF) provides a powerful and flexible framework for creating user interfaces in .NET applications. This section covers the essential aspects of WPF controls and layout management, with complete examples for each topic.

---

#### Common WPF Controls

WPF includes a wide range of built-in controls that can be used to build user interfaces. Some of the most commonly used controls are:

1. **Button**
   ```xml
   <Button Content="Click Me" Width="100" Height="30" Click="Button_Click"/>
   ```

2. **TextBox**
   ```xml
   <TextBox Width="200" Height="30" Text="Enter text here"/>
   ```

3. **ComboBox**
   ```xml
   <ComboBox Width="150">
       <ComboBoxItem Content="Option 1"/>
       <ComboBoxItem Content="Option 2"/>
       <ComboBoxItem Content="Option 3"/>
   </ComboBox>
   ```

4. **ListBox**
   ```xml
   <ListBox Width="200" Height="100">
       <ListBoxItem Content="Item 1"/>
       <ListBoxItem Content="Item 2"/>
       <ListBoxItem Content="Item 3"/>
   </ListBox>
   ```

5. **CheckBox**
   ```xml
   <CheckBox Content="Check me" IsChecked="True"/>
   ```

6. **RadioButton**
   ```xml
   <RadioButton GroupName="Group1" Content="Option A"/>
   <RadioButton GroupName="Group1" Content="Option B"/>
   ```

---

#### Layout Management

WPF provides several layout panels that help organize and position controls within the user interface. Below are examples of the most commonly used layout panels.

1. **Grid**

   The `Grid` panel divides the available space into rows and columns, allowing for complex layouts.

   ```xml
   <Grid>
       <Grid.RowDefinitions>
           <RowDefinition Height="Auto"/>
           <RowDefinition Height="*"/>
       </Grid.RowDefinitions>
       <Grid.ColumnDefinitions>
           <ColumnDefinition Width="Auto"/>
           <ColumnDefinition Width="*"/>
       </Grid.ColumnDefinitions>

       <TextBlock Text="Label:" Grid.Row="0" Grid.Column="0" Margin="5"/>
       <TextBox Grid.Row="0" Grid.Column="1" Margin="5"/>
       <Button Content="Submit" Grid.Row="1" Grid.ColumnSpan="2" Margin="5"/>
   </Grid>
   ```

2. **StackPanel**

   The `StackPanel` arranges its child elements into a single line that can be oriented horizontally or vertically.

   ```xml
   <StackPanel Orientation="Vertical" Margin="10">
       <TextBlock Text="Name:"/>
       <TextBox Width="200"/>
       <TextBlock Text="Age:"/>
       <TextBox Width="50"/>
   </StackPanel>
   ```

3. **DockPanel**

   The `DockPanel` arranges child elements by docking them to the top, bottom, left, or right edges of the panel.

   ```xml
   <DockPanel>
       <Button Content="Top" DockPanel.Dock="Top"/>
       <Button Content="Bottom" DockPanel.Dock="Bottom"/>
       <Button Content="Left" DockPanel.Dock="Left"/>
       <Button Content="Right" DockPanel.Dock="Right"/>
       <Button Content="Fill"/>
   </DockPanel>
   ```

4. **WrapPanel**

   The `WrapPanel` positions child elements in sequential order, breaking content to the next line at the edge of the containing box.

   ```xml
   <WrapPanel>
       <Button Content="Button 1" Width="100" Height="30"/>
       <Button Content="Button 2" Width="100" Height="30"/>
       <Button Content="Button 3" Width="100" Height="30"/>
       <Button Content="Button 4" Width="100" Height="30"/>
   </WrapPanel>
   ```

5. **Canvas**

   The `Canvas` panel allows absolute positioning of child elements, using `Canvas.Left` and `Canvas.Top` properties.

   ```xml
   <Canvas>
       <Button Content="Button 1" Canvas.Left="50" Canvas.Top="20"/>
       <Button Content="Button 2" Canvas.Left="150" Canvas.Top="60"/>
   </Canvas>
   ```

6. **UniformGrid**

   The `UniformGrid` divides the available space into equally sized cells, ensuring all child elements occupy the same amount of space.

   ```xml
   <UniformGrid Rows="2" Columns="3">
       <Button Content="Button 1"/>
       <Button Content="Button 2"/>
       <Button Content="Button 3"/>
       <Button Content="Button 4"/>
       <Button Content="Button 5"/>
       <Button Content="Button 6"/>
   </UniformGrid>
   ```

---

#### Custom Panels

In WPF, you can create custom panels by inheriting from `Panel` and overriding the `MeasureOverride` and `ArrangeOverride` methods. Here is an example of a custom circular panel:

```csharp
public class CircularPanel : Panel
{
    protected override Size MeasureOverride(Size availableSize)
    {
        Size size = new Size();
        foreach (UIElement child in InternalChildren)
        {
            child.Measure(availableSize);
            size.Width = Math.Max(size.Width, child.DesiredSize.Width);
            size.Height = Math.Max(size.Height, child.DesiredSize.Height);
        }
        return size;
    }

    protected override Size ArrangeOverride(Size finalSize)
    {
        double angle = 0;
        double increment = 360.0 / InternalChildren.Count;

        foreach (UIElement child in InternalChildren)
        {
            double x = finalSize.Width / 2 + Math.Cos(angle * Math.PI / 180) * (finalSize.Width / 4) - child.DesiredSize.Width / 2;
            double y = finalSize.Height / 2 + Math.Sin(angle * Math.PI / 180) * (finalSize.Height / 4) - child.DesiredSize.Height / 2;

            child.Arrange(new Rect(new Point(x, y), child.DesiredSize));
            angle += increment;
        }

        return finalSize;
    }
}
```

This custom panel arranges its children in a circular pattern.

---

#### Control Alignment and Margins

Control alignment and margins are essential for refining the appearance of a WPF user interface.

- **HorizontalAlignment and VerticalAlignment:**

   These properties control how a control is aligned within its parent container.

   ```xml
   <Button Content="Aligned Button" Width="100" Height="30" HorizontalAlignment="Right" VerticalAlignment="Top"/>
   ```

- **Margins:**

   The `Margin` property adds space around a control, which can be set individually for the left, top, right, and bottom sides.

   ```xml
   <Button Content="Button with Margin" Width="100" Height="30" Margin="10,20,10,0"/>
   ```

---

#### Z-Index in Layouts

The `ZIndex` property in WPF controls the rendering order of overlapping elements within the same container. A higher `ZIndex` value places an element in front of those with lower values.

```xml
<Canvas>
    <Rectangle Width="100" Height="100" Fill="Red" Canvas.Left="50" Canvas.Top="50" Panel.ZIndex="1"/>
    <Rectangle Width="100" Height="100" Fill="Blue" Canvas.Left="75" Canvas.Top="75" Panel.ZIndex="2"/>
    <Rectangle Width="100" Height="100" Fill="Green" Canvas.Left="100" Canvas.Top="100" Panel.ZIndex="3"/>
</Canvas>
```

In this example, the green rectangle will appear on top of the blue one, which in turn appears on top of the red one, based on their `ZIndex` values.
