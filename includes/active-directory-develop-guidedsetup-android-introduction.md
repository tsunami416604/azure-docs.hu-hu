
# <a name="call-the-microsoft-graph-api-from-an-android-app"></a>A Microsoft Graph API hívása az Android-alkalmazás

Ez az útmutató ismerteti, hogyan natív Android-alkalmazás szereznie egy hozzáférési jogkivonatot és hívható meg Microsoft Graph API vagy egyéb Azure Active Directory v2 végpont a hozzáférési jogkivonatok igénylő API-k.

Ez az útmutató végén az alkalmazás fogja tudni hívható meg egy védett API használatával személyes fiókok (például outlook.com, live.com és mások) valamint a munkahelyi és iskolai fiókok bármely vállalat vagy szervezet, amely Azure Active Directory.  

### <a name="how-this-sample-works"></a>Ez a minta működése
![Ez a minta működése](media/active-directory-develop-guidedsetup-android-intro/android-intro.png)

Ez az útmutató által létrehozott minta egy olyan forgatókönyvet, ahol egy Android-alkalmazás, amely az Azure Active Directory v2 végpont – ebben az esetben a Microsoft Graph API származó jogkivonatokat fogad el egy webes API lekérdezésére szolgál alapul. Ebben az esetben jogkivonat adni a hitelesítési fejlécéhez via HTTP-kérelmekre. Token beszerzése és -megújítás kezelése a Microsoft hitelesítési könyvtár (MSAL).

### <a name="pre-requisites"></a>Előfeltételek
* Ez az interaktív telepítés Android Studio összpontosít, de bármely olyan Android-alkalmazás fejlesztői környezetben is elfogadható. 
* Android SDK 21 vagy újabb rendszer szükséges (SDK 25 ajánlott).
* Google Chrome vagy egy webes böngésző egyéni lapok használatával szükség ebben a kiadásban Microsoft hitelesítési könyvtár (MSAL) Android rendszerhez.

> Megjegyzés: Google Chrome nem szerepel a Visual Studio-emulátor Android rendszerhez. Azt javasoljuk, hogy ez a kód egy emulátorának API 25 vagy lemezkép API 21 vagy újabb Google Chrome telepített tesztelése.


### <a name="how-to-handle-token-acquisition-to-access-a-protected-web-api"></a>Egy védett Web API eléréséhez token beszerzési kezelésének módját

Miután a felhasználó hitelesíti magát, a mintaalkalmazást kap egy jogkivonatot, amely segítségével lekérdezési Microsoft Graph API vagy egy Microsoft Azure Active Directory v2 által biztosított webes API.

Például a Microsoft Graph API-k igényelnek olyan hozzáférési jogkivonatot, hogy az adott erőforrások – például elérése egy profil, hozzáférés felhasználói naptár olvasni és e-mailt küldeni. Az alkalmazás egy MSAL API hatókörök megadásával ezek az erőforrások eléréséhez használt jogkivonatot kérhet. Ez a jogkivonat majd hozzáadódik a HTTP Authorization fejlécet minden hívás, szemben a védett erőforrásokhoz. 

MSAL kezeli, és a hozzáférési jogkivonatok frissítése, így nem kell az alkalmazást.

### <a name="libraries"></a>Szalagtárak

Ez az útmutató a következő tárakat használ:

|Részletes ismertetés|Leírás|
|---|---|
|[com.microsoft.Identity.Client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft hitelesítési könyvtár (MSAL)|
