### <a name="prerequisites"></a>Előfeltételek
* A Twilio-fiókja
* Ellenőrzött Twilio-telefonszámot, amelyet a SMS fogadására
* Az SMS küldő ellenőrzött Twilio telefonszám rendelve

> [!NOTE]
> Ha a Twilio-próbafiókra használ, az SMS csak elküldheti **ellenőrzése** telefonszámokat.  
> 
> 

A Twilio-fiókját a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás Twilio-fiókjához. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon. 

A Logic Apps alkalmazást Twilio-fiókja való csatlakozáshoz engedélyezni lépései a következők:

1. Válassza ki a Twilio-, VPN-kapcsolat létrehozásához a Logic app tervezőben, **megjelenítése Microsoft felügyelt API-k** a legördülő listában adja meg *Twilio* be a keresőmezőbe. Válassza ki az eseményindító vagy művelet fogjuk használni kívánt:  
   ![](./media/connectors-create-api-twilio/twilio-0.png)
2. Ha még nem hozott létre a kapcsolatokat a Twilio előtt, meg fogja kérni a Twilio-hitelesítő adatainak megadását. Ezek a hitelesítő adatok engedélyezik a Logic Apps alkalmazást való kapcsolódáshoz használandó, és hozzáférni a Twilio-fiókja adatai:  
   ![](./media/connectors-create-api-twilio/twilio-1.png)  
3. Szüksége lesz a **Twilio fiókazonosító** és **Twilio hozzáférési jogkivonat** Twilio az irányítópultról, jelentkezzen be a Twilio-fiókja most két adatot bejelentkezési:  
   ![](./media/connectors-create-api-twilio/twilio-2.png)  
4. Twilio és a Logic apps segítségével, hogy két adatot azonosíthatja a különböző neveket. Itt látható, hogyan kell társítani őket a Logic apps párbeszédpanel: ![](./media/connectors-create-api-twilio/twilio-3.png)  
5. Válassza ki a **hozható létre kapcsolat** gombra:  
   ![](./media/connectors-create-api-twilio/twilio-4.png)
6. Figyelje meg, a kapcsolat létrejött, és szabadon most folytatja a Logic Apps alkalmazást más lépéseket:  
   ![](./media/connectors-create-api-twilio/twilio-5.png)

