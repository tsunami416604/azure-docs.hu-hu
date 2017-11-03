#### <a name="prerequisites"></a>Előfeltételek
* Az Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* A [Dynamics CRM Online](https://www.microsoft.com/en-us/dynamics/crm-free-trial-overview.aspx) fiók 

Egy logikai alkalmazást a Dynamics fiók használatához engedélyezni, a logikai alkalmazás CRM Online-fiókjához. Ehhez egyszerűen a logikai alkalmazásban, az Azure portálon. 

A Logic Apps alkalmazást való csatlakozáshoz az alábbi lépéseket követve CRM Online-fiókját engedélyezi:

1. Logikai alkalmazás létrehozása. A Logic Apps-tervezőben, válassza ki a **megjelenítése Microsoft felügyelt API-k** a legördülő listában, és írja be a keresőmezőbe "dynamics". Az eseményindítók és műveletek közül választhat:  
   ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Ha korábban még nem létrehozott Dynamics fennálló kapcsolatokat, Dynamics hitelesítő adataival bejelentkezni kéri:  
   ![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. Válassza ki **bejelentkezés**, és írja be a felhasználónevet és jelszót. Válassza ki **bejelentkezés**. 
   
    Ezek a hitelesítő adatok segítségével engedélyezik a Logic Apps alkalmazást csatlakozni, és a Dynamics fiókban tárolt adatok eléréséhez. 
4. Figyelje meg, a kapcsolat létrejött. Most folytassa a Logic Apps alkalmazást más lépéseket:  
   ![](./media/connectors-create-api-crmonline/dynamics-properties.png)

