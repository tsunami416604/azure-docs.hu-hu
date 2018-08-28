### <a name="prerequisites"></a>Előfeltételek
* A [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) fiók  

Az SMTP-fiókja, a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás az SMTP-fiókhoz való csatlakozáshoz. Szerencsére a ehhez egyszerűen, a logikai alkalmazás az Azure Portal webhelyen belül.  

A logikai alkalmazás az SMTP-fiókhoz való csatlakozás engedélyezése lépései a következők:  

1. A logic app Designerben az SMTP-kapcsolat létrehozásához jelölje ki a **megjelenítése a Microsoft által felügyelt API-k** a legördülő listában adja meg *SMTP* kifejezést a keresőmezőbe. Válassza ki az eseményindítót vagy műveletet fogja kíván használni:  
   ![](./media/connectors-create-api-smtp/smtp-1.png)  
2. Ha még nem hozott létre, mielőtt SMTP kapcsolatokat, első kéri az SMTP hitelesítő adatait. Ezekkel a hitelesítő adatokkal kapcsolódni a logikai alkalmazás hitelesítéséhez használható, és az SMTP-fiókja adatainak eléréséhez:  
   ![](./media/connectors-create-api-smtp/smtp-2.png)  
3. Figyelje meg, hogy a kapcsolat létrejött, és most már szabadon folytassa a további lépésekkel, a logikai alkalmazásban:  
   ![](./media/connectors-create-api-smtp/smtp-3.png)  

