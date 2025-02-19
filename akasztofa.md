# Akasztófa játék WPF-ben – Magyarázat

Ez a program egy egyszerű „Akasztófa” játék WPF (Windows Presentation Foundation) környezetben, ahol a felhasználónak egy véletlenszerűen kiválasztott szót kell kitalálnia betűnként.

Az alábbiakban részletesen bemutatjuk a program felépítését és működését.

## **1. XAML felépítés (Felhasználói felület)**

A `MainWindow.xaml` fájl határozza meg a játék grafikus felületét.

### **1.1 Ablak és alapbeállítások**
```xml
<Window x:Class="Akasztofa.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="Akasztófa" Height="350" Width="400">
Title="Akasztófa" – Az ablak címe.
Height="350" Width="400" – Az ablak mérete.
1.2 Fő komponensek
A felhasználói felület egy Grid elrendezésen belül helyezkedik el, amely tartalmazza az alábbi elemeket:

##1.2.1 Szó megjelenítése
xml
Másolás
Szerkesztés
<TextBlock Text="Keresett szó:" Margin="10,10,0,0" VerticalAlignment="Top"/>
<TextBlock Name="SzoKijelzo" FontSize="24" Margin="10,30,10,0" Text="_ _ _ _" />
Célja: Megjeleníti a kitalálandó szót alulvonásokkal (_ _ _ _).
Name="SzoKijelzo" – Ezen keresztül módosítja a program a szó állapotát.
##1.2.2 Betű bevitele
xml
Másolás
Szerkesztés
<TextBlock Text="Adj meg egy betűt:" Margin="10,80,0,0" VerticalAlignment="Top"/>
<TextBox Name="BetuBevitel" Width="30" Height="30" Margin="150,80,0,0" VerticalAlignment="Top"/>
<Button Content="Tipp" Width="75" Height="30" Margin="200,80,10,0" VerticalAlignment="Top" Click="TippGomb_Kattintas"/>
TextBox Name="BetuBevitel" – Itt adja meg a felhasználó a betűt.
Button Content="Tipp" – Gomb a tipp elküldésére.
Click="TippGomb_Kattintas" – Az eseménykezelő metódus, amely a betű ellenőrzését végzi.
##1.2.3 Rossz tippek kijelzése
xml
Másolás
Szerkesztés
<TextBlock Text="Rossz tippek:" Margin="10,130,0,0" VerticalAlignment="Top"/>
<TextBlock Name="RosszTippek" Margin="10,150,10,0" FontSize="16"/>
RosszTippek – Megjeleníti az elhibázott próbálkozásokat.
##2. C# kód (MainWindow.xaml.cs)
A MainWindow.xaml.cs fájl tartalmazza a program logikáját.

##2.1 Osztály és mezők deklarálása
csharp
Másolás
Szerkesztés
public partial class MainWindow : Window
{
    private string[] szavak = { "alma", "körte", "citrom", "barack", "eper" };
    private string kivalasztottSzo;
    private char[] kitalaltSzo;
    private int rosszProbalkozasok = 0;
    private string rosszBetuk = "";
szavak – A lehetséges szavak listája.
kivalasztottSzo – A véletlenszerűen kiválasztott szó.
kitalaltSzo – A játék során kitalált betűket tartalmazó karaktertömb.
rosszProbalkozasok – Az elhibázott próbálkozások száma.
rosszBetuk – Az eddig elhibázott betűk.
##2.2 Játék indítása
csharp
Másolás
Szerkesztés
public MainWindow()
{
    InitializeComponent();
    UjJatek();
}

private void UjJatek()
{
    Random veletlen = new Random();
    kivalasztottSzo = szavak[veletlen.Next(szavak.Length)];
    kitalaltSzo = new string('_', kivalasztottSzo.Length).ToCharArray();
    rosszProbalkozasok = 0;
    rosszBetuk = "";
    RosszTippek.Text = "";
    FrissitSzo();
}
UjJatek() – Új játék indítása, kiválaszt egy véletlenszerű szót, és alaphelyzetbe állítja a változókat.
FrissitSzo() – Frissíti a szó kijelzését.
##2.3 Tipp feldolgozása
csharp
Másolás
Szerkesztés
private void TippGomb_Kattintas(object sender, RoutedEventArgs e)
{
    if (BetuBevitel.Text.Length == 1)
    {
        char betu = BetuBevitel.Text.ToLower()[0];

        if (kivalasztottSzo.Contains(betu))
        {
            for (int i = 0; i < kivalasztottSzo.Length; i++)
            {
                if (kivalasztottSzo[i] == betu)
                {
                    kitalaltSzo[i] = betu;
                }
            }
        }
        else
        {
            if (!rosszBetuk.Contains(betu))
            {
                rosszProbalkozasok++;
                rosszBetuk += betu + " ";
                RosszTippek.Text = rosszBetuk;
            }
        }

        FrissitSzo();
        EllenorizJatekAllapot();
    }

    BetuBevitel.Text = "";
}
Ellenőrzi, hogy a bevitt betű szerepel-e a szóban.
Ha igen, beírja a megfelelő helyre.
Ha nem, hozzáadja a rossz próbálkozások listájához.
Minden tipp után frissíti a szó kijelzését és ellenőrzi a játék állapotát.
##2.4 Játék állapotának ellenőrzése
csharp
Másolás
Szerkesztés
private void EllenorizJatekAllapot()
{
    if (!kitalaltSzo.Contains('_'))
    {
        MessageBox.Show("Gratulálok! Kitaláltad a szót: " + kivalasztottSzo);
        UjJatek();
    }
    else if (rosszProbalkozasok >= 6)
    {
        MessageBox.Show("Vesztettél! A szó ez volt: " + kivalasztottSzo);
        UjJatek();
    }
}
Ha a játékos kitalálta az összes betűt, kiírja a nyertes üzenetet és új játékot indít.
Ha 6 rossz próbálkozása van, vesztett, és új játék indul.
##3. Összegzés
WPF-es felületet használunk a játék megjelenítésére.
Véletlenszerűen választunk egy szót az adott listából.
A felhasználó betűnként próbálkozhat a szó megfejtésével.
Rossz tippek számlálása: ha 6 rossz próbálkozás összegyűlik, a játékos veszít.
Ha minden betűt kitalál, a játékos nyer.
Ez a program egy egyszerű, de jól működő akasztófa játék, amelyet tovább lehet fejleszteni például grafikus akasztófa kirajzolásával vagy további nehézségi szintekkel.