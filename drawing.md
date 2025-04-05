# Canvas + Drawing
 A Canvas segítségével nagyon egyszerűen tudunk painthez hasonló rajzoló programot készíteni.
 A működése a következő:
## 1. Canvas létrehozása
- Létrehozunk egy Canvast, amire fontos hogy állítsunk Backgroundot hogy kattintható legyen a területére, illetve meghívunk három függvényt ami a rajzolás folyamatát fogja vezetni.
```wpf
<Canvas Name="MyCanvas" Background="White" MouseDown="MyCanvas_MouseDown" MouseMove="MyCanvas_MouseMove" MouseUp="MyCanvas_MouseUp" Grid.Row="1"/>
```
## 2. Canvason az alap működéshez kötelező függvények
Ez a három függvény a következő: MouseDown, MouseMove, MouseUp
A működésük nagyon egyszerű:
### MouseDown
-  Érzékeli amikor a Canvas területe felett lenyomod a gombot, elindítja a rajzolás folyamatát.
### MouseMove
- Ez a függvény akkor lesz meghívva, ha a canvas területén mozgatjuk az egeret, ez a MouseDown-ban létrehozott vonalat fogjuk a kurzor pozíciójára helyezni folyamatosan.
### MouseUp
- Ez a függvény pedig bejefezi a vonal rajzolása folyamatát.
Kód:
```csharp
private void MyCanvas_MouseDown(object sender, MouseButtonEventArgs e)
{
    isDrawing = true;
    currentLine = new Polyline
    {
        Stroke = selectedColor,
        StrokeThickness = lineThickness
    };
    MyCanvas.Children.Add(currentLine);
}

private void MyCanvas_MouseMove(object sender, MouseEventArgs e)
{
    if (isDrawing && currentLine != null)
    {
        currentLine.Points.Add(e.GetPosition(MyCanvas));
    }
}

private void MyCanvas_MouseUp(object sender, MouseButtonEventArgs e)
{
    isDrawing = false;
    currentLine = null; 
}
```
## 3. Változók az előző és a következő függvények működéséhez
Ezeknek a működéséhez fel kell venni pár változót amik a következőek:
```csharp
private bool isDrawing = false; 
private Polyline currentLine;    
private int lineThickness;
private Brush selectedColor = Brushes.Black;
```
## 4. Vonalat és a Canvast konfiguráló függvények
Én három féle ilyen függvényt írtam, amihez először felvettem egy slidert, egy comboboxot, illetve egy gombot.
Ezeket a vonal vastagságára, vonal színére használjuk, illetve a Canvas törlésére.
```csharp
    private void ThicknessValueSlider(object sender, RoutedPropertyChangedEventArgs<double> e)
    {
        if (sender is Slider slider)
        {
            lineThickness = Convert.ToInt32(slider.Value);
        }
    }

    private void ClearCanvas(object sender, RoutedEventArgs e)
    {
        MyCanvas.Children.Clear();
        lineThickness = 2;
        selectedColor = Brushes.Black;
        LineWidthSlider.Value = 2;
        ColorPicker.SelectedIndex = 0;
}
    private void ColorPicker_SelectionChanged(object sender, SelectionChangedEventArgs e)
    {
        if (ColorPicker.SelectedItem is ComboBoxItem selectedItem)
        {
            string colorName = selectedItem.Tag.ToString();
            //selectedColor = (Brush)new BrushConverter().ConvertFromString(colorName);
            switch (colorName)
            {
                case "black":
                    selectedColor = Brushes.Black;
                    break;
                case "blue":
                    selectedColor = Brushes.Blue;
                    break;
                case "green":
                    selectedColor = Brushes.Green;
                    break;
                case "red":
                    selectedColor = Brushes.Red;
                    break;
                default:
                    break;
            }
        }
    }
```
## 5. Felvesszük ezeket az inputokat az xaml-be is
- Felveszünk egy stackpanelt, amiben találhatóak majd az inputok
- Felvesszük a textblockokat felíratként, illetve az inputokat inputoknak
```wpf
<Rectangle Fill="LightGray" Grid.Row="0"/>
<StackPanel Orientation="Horizontal" VerticalAlignment="Center" HorizontalAlignment="Center">
    <TextBlock Text="Vastagság: " Grid.Row="0"/>
    <Slider x:Name="LineWidthSlider" Minimum="2" Maximum="32" Value="2" IsSnapToTickEnabled="True" Grid.Row="0" Width="200" ValueChanged="ThicknessValueSlider"/>
    <TextBlock Text="Válassz színt: "/>
    <ComboBox x:Name="ColorPicker" SelectionChanged="ColorPicker_SelectionChanged" Width="65">
        <ComboBoxItem Content="Fekete" Tag="black" IsSelected="True"/>
        <ComboBoxItem Content="Piros" Tag="red"/>
        <ComboBoxItem Content="Kék" Tag="blue"/>
        <ComboBoxItem Content="Zöld" Tag="green"/>
    </ComboBox>
    <Button Content="Törlés" Click="ClearCanvas" Margin="10,0,0,0"/>
</StackPanel>
```

<details>
  <summary>MainWindow.xaml</summary>

  ```wpf
  <Window x:Class="DrawingApp.MainWindow"
      xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
      xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
      Title="Egyszerű Rajzoló" Height="450" Width="800">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="50"/>
            <RowDefinition/>
        </Grid.RowDefinitions>
        <Rectangle Fill="LightGray" Grid.Row="0"/>
        <StackPanel Orientation="Horizontal" VerticalAlignment="Center" HorizontalAlignment="Center">
            <TextBlock Text="Vastagság: " Grid.Row="0"/>
            <Slider x:Name="LineWidthSlider" Minimum="2" Maximum="32" Value="2" IsSnapToTickEnabled="True" Grid.Row="0" Width="200" ValueChanged="ThicknessValueSlider"/>
            <TextBlock Text="Válassz színt: "/>
            <ComboBox x:Name="ColorPicker" SelectionChanged="ColorPicker_SelectionChanged" Width="65">
                <ComboBoxItem Content="Fekete" Tag="black" IsSelected="True"/>
                <ComboBoxItem Content="Piros" Tag="red"/>
                <ComboBoxItem Content="Kék" Tag="blue"/>
                <ComboBoxItem Content="Zöld" Tag="green"/>
            </ComboBox>
            <Button Content="Törlés" Click="ClearCanvas" Margin="10,0,0,0"/>
        </StackPanel>

        <Canvas Name="MyCanvas" Background="White" MouseDown="MyCanvas_MouseDown" MouseMove="MyCanvas_MouseMove" MouseUp="MyCanvas_MouseUp" Grid.Row="1"/>
    </Grid>
</Window>


  ```
</details>

<details>
  <summary>MainWindow.xaml.cs</summary>

  ```csharp
  using System.Windows;
using System.Windows.Controls;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Shapes;

namespace DrawingApp
{
    public partial class MainWindow : Window
    {
        private bool isDrawing = false; 
        private Polyline currentLine;    
        private int lineThickness;
        private Brush selectedColor = Brushes.Black;

        public MainWindow()
        {
            InitializeComponent();
        }

        private void MyCanvas_MouseDown(object sender, MouseButtonEventArgs e)
        {
            isDrawing = true;
            currentLine = new Polyline
            {
                Stroke = selectedColor,
                StrokeThickness = lineThickness
            };
            MyCanvas.Children.Add(currentLine);
        }

        private void MyCanvas_MouseMove(object sender, MouseEventArgs e)
        {
            if (isDrawing && currentLine != null)
            {
                currentLine.Points.Add(e.GetPosition(MyCanvas));
            }
        }

        private void MyCanvas_MouseUp(object sender, MouseButtonEventArgs e)
        {
            isDrawing = false;
            currentLine = null; 
        }

        private void ThicknessValueSlider(object sender, RoutedPropertyChangedEventArgs<double> e)
        {
            if (sender is Slider slider)
            {
                lineThickness = Convert.ToInt32(slider.Value);
            }
        }

        private void ClearCanvas(object sender, RoutedEventArgs e)
        {
            MyCanvas.Children.Clear();
            lineThickness = 2;
            selectedColor = Brushes.Black;
            LineWidthSlider.Value = 2;
            ColorPicker.SelectedIndex = 0;
    }
        private void ColorPicker_SelectionChanged(object sender, SelectionChangedEventArgs e)
        {
            if (ColorPicker.SelectedItem is ComboBoxItem selectedItem)
            {
                string colorName = selectedItem.Tag.ToString();
                //selectedColor = (Brush)new BrushConverter().ConvertFromString(colorName);
                switch (colorName)
                {
                    case "black":
                        selectedColor = Brushes.Black;
                        break;
                    case "blue":
                        selectedColor = Brushes.Blue;
                        break;
                    case "green":
                        selectedColor = Brushes.Green;
                        break;
                    case "red":
                        selectedColor = Brushes.Red;
                        break;
                    default:
                        break;
                }
            }
        }
    }
}

  ```
</details>
