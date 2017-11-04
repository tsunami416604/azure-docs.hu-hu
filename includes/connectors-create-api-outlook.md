## <a name="connect-to-outlookcom"></a>Outlook.com-os csatlakozás
### <a name="prerequisites"></a>Előfeltételek
* Egy Outlook.com-fiók

Outlook.com-os fiókját a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás Outlook.com-os fiókjához. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon. 

A Logic Apps alkalmazást Outlook.com-os fiókjához történő engedélyezéséhez lépései a következők:

1. Logic Apps-alkalmazások összes el kell indítani egy eseményindító által, miután hoz létre a Logic Apps alkalmazást, a Tervező megnyílik, és megjeleníti váltja ki, amely segítségével indítsa el a logikai alkalmazás:
   
   ![](./media/connectors-create-api-outlook/office365-outlook-0.png)
2. A keresőmezőbe írja be a "outlook". Figyelje meg, a szűrt "Outlook" az eseményindítók listát a nevében a lista:![](./media/connectors-create-api-outlook/office365-outlook-0-5.png)
3. Válassza ki **Office 365 Outlook - az új e-mailek**.   
   Ha még nem hozott létre a kapcsolatokat, az Outlook programot, a rendszer az Outlook.com-os hitelesítő fog kérni. Ezek a hitelesítő adatok engedélyezik a Logic Apps alkalmazást való kapcsolódáshoz használt, és elérni az Outlook.com-os fiók adatait:![](./media/connectors-create-api-outlook/office365-outlook-1.png)
4. Adja meg a hitelesítő adatait az Outlook, és jelentkezzen be:![](./media/connectors-create-api-outlook/office365-outlook-2.png)  
   Ennyi az egész. Most létrehozott egy kapcsolatot Outlook. Ez a kapcsolat bármely más logikai alkalmazás az Ön által létrehozott használható lesz.

