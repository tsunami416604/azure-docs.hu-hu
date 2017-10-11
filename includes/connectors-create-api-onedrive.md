#### <a name="prerequisites"></a>Előfeltételek
* Az Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* A [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) fiók 

A OneDrive-fiókja a logikai alkalmazás használata előtt engedélyezze a logikai alkalmazást a OneDrive-fiókja való kapcsolódáshoz.  Ehhez egyszerűen a logikai alkalmazásban, az Azure portálon. 

Engedélyezze a Logic Apps alkalmazást, a OneDrive-fiókja, az alábbi lépéseket követve való csatlakozáshoz:

1. Logikai alkalmazás létrehozása. A Logic Apps-tervezőben, válassza ki a **megjelenítése Microsoft felügyelt API-k** a legördülő listában, és írja be a keresőmezőbe "onedrive". Az eseményindítók és műveletek közül választhat:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Ha korábban még nem létrehozott a onedrive-kapcsolatokat, a onedrive vállalati verzió hitelesítő adataival bejelentkezni kéri:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Válassza ki **bejelentkezés**, és írja be a felhasználónevet és jelszót. Válassza ki **bejelentkezés**:  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Ezek a hitelesítő adatok segítségével engedélyezi a Logic Apps alkalmazást, és a OneDrive-fiókja adatait. 
4. Válassza ki **Igen** engedélyezése a logikai alkalmazás használata a OneDrive-fiókja:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Figyelje meg, a kapcsolat létrejött. Most folytassa a Logic Apps alkalmazást más lépéseket:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

