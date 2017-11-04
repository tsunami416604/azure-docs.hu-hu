### <a name="prerequisites"></a>Előfeltételek
* A [MicrosoftTranslator](https://www.microsoft.com/translator) fiók  

A MicrosoftTranslator fiókját a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás MicrosoftTranslator fiókjához. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon.  

A Logic Apps alkalmazást MicrosoftTranslator fiókjához történő engedélyezéséhez lépései a következők:  

1. A Logic app designer MicrosoftTranslator, kapcsolat létrehozásához válassza **megjelenítése Microsoft felügyelt API-k** a legördülő listában adja meg *MicrosoftTranslator* be a keresőmezőbe. Válassza ki az eseményindító vagy művelet fogjuk használni kívánt:  
   ![MicrosoftTranslator kapcsolat létrehozását lépést](./media/connectors-create-api-microsofttranslator/microsofttranslator-1.png)  
2. Ha még nem hozott létre előtt MicrosoftTranslator fennálló kapcsolatokat, a rendszer MicrosoftTranslator hitelesítő adatok megadása fog kérni. Ezek a hitelesítő adatok engedélyezik a Logic Apps alkalmazást való kapcsolódáshoz használandó, és MicrosoftTranslator fiókja adatok eléréséhez:  
   ![MicrosoftTranslator kapcsolat létrehozását lépést](./media/connectors-create-api-microsofttranslator/microsofttranslator-2.png)  
3. Figyelje meg, a kapcsolat létrejött, és szabadon most folytatja a Logic Apps alkalmazást más lépéseket:  
   ![MicrosoftTranslator kapcsolat létrehozását lépést](./media/connectors-create-api-microsofttranslator/microsofttranslator-3.png)  

