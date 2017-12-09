
## <a name="set-up-your-project"></a>A projekt beállítása

Ez a szakasz részletesen arról, hogyan hozzon létre egy új projektet bemutatják, hogyan integrálhatja a Windows asztali .NET-alkalmazás (XAML) rendelkező *jelentkezzen be Microsoft* azt lekérdezhesse jogkivonat szükséges, webes API-k.

Ez az útmutató által létrehozott alkalmazás közzététele egy diagramot és az eredmények képernyőn és kijelentkezési gomb megjelenítése gombra.

> Ez a minta Visual Studio-projekt letöltése helyette inkább? [Töltse le a projekt](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip) és ugorjon a [konfigurációs lépés](#create-an-application-express) konfigurálása a kódminta végrehajtása előtt.


### <a name="create-your-application"></a>Az alkalmazás létrehozása
1. A Visual Studio:`File` > `New` > `Project`<br/>
2. A *sablonok*, jelölje be`Visual C#`
3. Válassza ki `WPF App` (vagy *WPF-alkalmazás* attól függően, hogy a Visual Studio verziója)

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>A Microsoft hitelesítési könyvtár (MSAL) hozzáadása a projekthez
1. A Visual Studio:`Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Másolja és illessze be a következő, a Package Manager Console ablakban:

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

> A fenti csomag telepíti a Microsoft hitelesítési könyvtár (MSAL). MSAL kezeli az beszerzése, gyorsítótárazás, és védi az Azure Active Directory v2 API-k elérésére használt felhasználói toskens frissítésekor.

## <a name="add-the-code-to-initialize-msal"></a>Adja hozzá a kódot MSAL inicializálása
Ez a lépés az hozzon létre egy osztályt, például jogkivonatokat kezelésének MSAL könyvtárban való együttműködéshez kezeléséhez nyújt segítséget.

1. Nyissa meg a `App.xaml.cs` fájlt, és a hivatkozás MSAL szalagtár osztályra:

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Frissítse az alkalmazás osztály a következőhöz:
</li>
</ol>

```csharp
public partial class App : Application
{
    //Below is the clientId of your app registration. 
    //You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";

    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);

}
```

## <a name="create-your-applications-ui"></a>Az alkalmazás felhasználói Felületüket létrehozni
Az alábbi szakasz bemutatja, hogyan tudja lekérdezni egy alkalmazás például a Microsoft Graph védett háttérkiszolgálóhoz. Egy MainWindow.xaml fájl automatikusan a projekt sablon részeként hozható létre. Ezt a fájlt megnyitni a fájlt, és kövesse az alábbi utasításokat:

Cserélje le az alkalmazás `<Grid>` a következő lesz:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```
