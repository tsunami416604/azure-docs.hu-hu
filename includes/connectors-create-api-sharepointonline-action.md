Most, hogy hozzáadta egy eseményindítót, idejének módosításával az eseményindító által generált adatok érdekes. Kövesse a fenti lépésekkel felvette a **SharePoint Online - fájl létrehozása** művelet. Ez a művelet létrehoz egy fájlt a SharePoint Online minden alkalommal, amikor az új elem eseményindító következik be. 

Szerint konfigurálhatja ezt a műveletet, akkor adja meg a következő információkat. Mivel ezek az információk láthatja, hogy a rendszer az egyes tulajdonságok az új fájl bemeneteként az eseményindító által létrehozott könnyen használható adatokat:

| Fájl tulajdonság létrehozása | Leírás |
| --- | --- |
| Webhely URL-címe |Ez az URL-CÍMÉT a SharePoint Online helyet, ahol az új fájl létrehozásához. Válassza ki a helyet a listáról, jelenik meg. |
| Mappa elérési útja |Ez az a mappa (webhely URL-címen az előző lépésben kiválasztott) lesz látható, ahová az új fájl kerül. Tallózással keresse meg és válassza ki a mappát. |
| Fájlnév |Azt a nevet, a fájl létrehozása folyamatban. |
| A fájl |A tartalom, lehet írni a fájlt. |

1. Válassza ki **+ új lépés** hozzáadása a műveletet.  
   ![SharePoint online művelet kép 1](./media/connectors-create-api-sharepointonline/action-1.png)  
2. Válassza ki a **művelet hozzáadása** hivatkozásra. Ez a keresési mezőbe, ahol kereshet bármilyen műveletet meg szeretné igénybe megnyílik. Ebben a példában a SharePoint-műveletek végezhetők, iránt.    
   ![SharePoint online művelet kép 2](./media/connectors-create-api-sharepointonline/action-2.png)    
3. Adja meg *sharepoint* SharePoint kapcsolatos műveletek kereséséhez.
4. Válassza ki **SharePoint Online - fájl létrehozása** , a végrehajtandó műveletet.   **Megjegyzés:**: kérni fogja a Logic Apps alkalmazást, a SharePoint-fiók eléréséhez, ha nem hozott létre kapcsolatot a SharePoint online korábban engedélyezésére.    
   ![SharePoint online művelet kép 3](./media/connectors-create-api-sharepointonline/action-3.png)    
5. A **létrehozás fájl** megnyílik szabályozzák.   
   ![SharePoint online művelet kép 4](./media/connectors-create-api-sharepointonline/action-4.png)     
6. Válassza ki **webhely URL-címe** tallózzon a hely hol szeretné létrehozni a fájlt.     
   ![SharePoint online művelet kép 5](./media/connectors-create-api-sharepointonline/action-5.png)  
7. Válassza ki **mappa elérési útja** , és keresse meg a mappát, ahová az új fájl kerül majd.  
   ![SharePoint online művelet kép 6](./media/connectors-create-api-sharepointonline/action-6.png)  
8. Válassza ki a **Fájlnév** vezérlik, és adja meg a létrehozni kívánt fájl nevét. Itt vagy adhatja meg a fájlnevet közvetlenül, vagy használhatja a korábban létrehozott eseményindítóval tulajdonságok. Ehhez válassza a tulajdonságok közül **kimeneteinek új elem létrehozásakor**. A lista létrehozási csak megjelenítési, miután kiválasztotta a **Fájlnév** vezérlő. A walkthough a kiválasztott ID (az új listaelem azonosítója) által létrehozott fájl neveként a **SharePoint Online - fájl létrehozása** művelet.    
   ![SharePoint online művelet kép 7](./media/connectors-create-api-sharepointonline/action-7.png)  
9. Válassza ki a **tartalom fájl** vezérlik, és adja meg a tartalom, lehet írni a fájlt, amely létrehozza. A fájl tartalmát a használt tulajdonságok a korábban létrehozott eseményindítóval figyelje meg. Egyszerűen válassza a Tulajdonságok jelenik meg a listából. Akkor is, megadhatja a **tartalom fájl** szöveg közvetlenül a vezérlőbe. Ebben a példában I kijelölt egyes tulajdonságok és területek és az egyes tulajdonságokhoz kötőjelet hozzá.        
   ![SharePoint online művelet kép 8](./media/connectors-create-api-sharepointonline/action-8.png)  
10. Mentse a módosításokat a munkafolyamat  
11. Gratulálunk, most már rendelkezik egy teljesen működőképes logikai alkalmazás, amely lesz kiváltva, ha egy új elemet hozzá van adva egy SharePoint Online listájához. Az alkalmazás ezután létrehoz egy fájlt, az új listaelem tulajdonságok használatával.  Most tesztelheti, hozzon létre egy új elemet a SharePoint-listán. 

