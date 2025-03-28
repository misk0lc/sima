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
    isDrawing = true; //Lejegyezzük hogy éppen rajzolunk
    currentLine = new Polyline //Létrehozunk egy vonalat. A polyline egy pontok által van felépítve, így ha sok van egymás mellett egy vonalnak néz ki.
    {
        Stroke = selectedColor, //Vonal színe
        StrokeThickness = lineThickness //Vonal vastagsága
    };
    currentLine.Points.Add(e.GetPosition(MyCanvas)); //A vonalnak létrehozunk egy pontot a vonalon, aminek a pozíciója egyenlő jelenleg a kurzor pozíciójának a Canvason (CurrentLine = vonal) (e.GetPosition(MyCanvas) = Kurzor pozíciója a Canvas területén) (E = kurzor)
    MyCanvas.Children.Add(currentLine); // A Canvashoz hozzá adjuk a vonal kezdőpontját
}

private void MyCanvas_MouseMove(object sender, MouseEventArgs e)
{
    if (isDrawing && currentLine != null) //Ha lenyomtuk a bal gombot (Ha rajzolunk) és ha létezik jelenlegi vonal akkor...
    {
        currentLine.Points.Add(e.GetPosition(MyCanvas)); //A MouseDown függvényhez azonosan hozunk létre a vonalon egy pontot, ami a kurzor pozíciójára helyezzük, viszont ez a függvény folyamatosan lefut amíg lenyomva tartjuk a gombot így egy folyamatos vonalat kapunk
    }
}

private void MyCanvas_MouseUp(object sender, MouseButtonEventArgs e)
{
    isDrawing = false; //Mivel elengedtük a bal gombot így befejezzük a rajzolás folymatát, így a MouseMove jelet kap hogy ne csináljon több pontot a vonalon
    currentLine = null; // Lezárjuk a vonalat
}
```
## 3. Változók az előző és a következő függvények működéséhez
Ezeknek a működéséhez fel kell venni pár változót amik a következőek:
```csharp
private bool isDrawing = false;  // Nyomon követi, hogy rajzolunk-e
private Polyline currentLine;    // Vonal amit "húzni" fogunk
private int lineThickness;    // Ez beállítja a vonalnak a vastagságát
private Brush selectedColor = Brushes.Black;    // Ez beállítja a vonalnak a színét (Alapból fekete)
```
## 4. Vonalat és a Canvast konfiguráló függvények
Én három féle ilyen függvényt írtam, amihez először felvettem egy slidert, egy comboboxot, illetve egy gombot.
Ezeket a vonal vastagságára, vonal színére használjuk, illetve a Canvas törlésére.
```csharp
private void ThicknessValueSlider(object sender, RoutedPropertyChangedEventArgs<double> e)
{
    if (sender is Slider slider)
    {
        lineThickness = Convert.ToInt32(slider.Value); // A slider értékét átkonvertáljuk Int-re és átadjuk a megfelelő változónak
    }
}

private void ClearCanvas(object sender, RoutedEventArgs e)
{
    MyCanvas.Children.Clear(); // Kitöröljük a Canvason létrehozott vonalakat amiket hozzáadtunk
}
private void ColorPicker_SelectionChanged(object sender, SelectionChangedEventArgs e)
{
    if (ColorPicker.SelectedItem is ComboBoxItem selectedItem)
    {
        string colorName = selectedItem.Tag.ToString(); //Lekérjük a comboboxitem tagját ami a szín neve van beállítva angolul
        selectedColor = (Brush)new BrushConverter().ConvertFromString(colorName); //Átkonvertáljuk a felső stringet egy Brush-ra hogy tudjuk használni színezésre, amit beletöltjük a megfelelő változóba
    }
}
```
## 5. Felvesszük ezeket az inputokat az xaml-be is
- Felveszünk egy stackpanelt, amiben találhatóak majd az inputok
- Felvesszük a textblockokat felíratként, illetve az inputokat inputoknak
```wpf
<StackPanel Orientation="Horizontal">
    <TextBlock Text="Vastagság: " Grid.Row="0"/>
    <Slider Minimum="2" Maximum="32" Value="2" IsSnapToTickEnabled="True" Grid.Row="0" Width="200" ValueChanged="ThicknessValueSlider"/>
    <TextBlock Text="Válassz színt: "/>
    <ComboBox Name="ColorPicker" SelectionChanged="ColorPicker_SelectionChanged" Width="100">
        <ComboBoxItem Content="Fekete" Tag="Black" IsSelected="True"/>
        <ComboBoxItem Content="Piros" Tag="Red"/>
        <ComboBoxItem Content="Kék" Tag="Blue"/>
        <ComboBoxItem Content="Zöld" Tag="Green"/>
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
        <StackPanel Orientation="Horizontal">
            <TextBlock Text="Vastagság: " Grid.Row="0"/>
            <Slider Minimum="2" Maximum="32" Value="2" IsSnapToTickEnabled="True" Grid.Row="0" Width="200" ValueChanged="ThicknessValueSlider"/>
            <TextBlock Text="Válassz színt: "/>
            <ComboBox Name="ColorPicker" SelectionChanged="ColorPicker_SelectionChanged" Width="100">
                <ComboBoxItem Content="Fekete" Tag="Black" IsSelected="True"/>
                <ComboBoxItem Content="Piros" Tag="Red"/>
                <ComboBoxItem Content="Kék" Tag="Blue"/>
                <ComboBoxItem Content="Zöld" Tag="Green"/>
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
        private bool isDrawing = false;  // Nyomon követi, hogy rajzolunk-e
        private Polyline currentLine;    // Az aktuális vonal
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
                Stroke = selectedColor, // Vonal színe fekete
                StrokeThickness = lineThickness     // Vonal vastagsága
            };
            currentLine.Points.Add(e.GetPosition(MyCanvas));
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
            currentLine = null; // Lezárjuk az aktuális vonalat
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
        }
        private void ColorPicker_SelectionChanged(object sender, SelectionChangedEventArgs e)
        {
            if (ColorPicker.SelectedItem is ComboBoxItem selectedItem)
            {
                string colorName = selectedItem.Tag.ToString();
                selectedColor = (Brush)new BrushConverter().ConvertFromString(colorName);
            }
        }
    }
}

  ```
</details>
