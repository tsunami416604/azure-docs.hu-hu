Most, hogy hozzáadott egy eseményindítót, valamit a idő érdekes, az eseményindító által generált adatokkal. Hajtsa végre a következő lépésekkel adhatja hozzá a **SharePoint Online - fájl létrehozása** művelet. Ez a művelet létrehoz egy fájlt a SharePoint online-ban minden alkalommal, amikor az új elem eseményindítót. 

Ennek konfigurálásához a művelet, szüksége lesz a következő információkat biztosítja. Ezt az információt ad meg, mivel láthatja, hogy-e az új fájl tulajdonságok némelyike bemenetként az eseményindító által létrehozott könnyen használható adatokat:

| Fájltulajdonság létrehozása | Leírás |
| --- | --- |
| Webhely URL-címe |Ez az URL-címét a SharePoint online-hoz helyet, ahol az új fájl létrehozásához. A megjelenő listából válassza ki a helyet. |
| Mappa elérési útja |Ez az a mappa (webhely URL-címen az előző lépésben kiválasztott), az új fájl kerül. Keresse meg és válassza ki a mappát. |
| Fájlnév |Ez a a létrehozandó fájl neve. |
| Fájl tartalma |A tartalom, amely a fájlba lesz írva. |

1. Válassza ki **+ új lépés** , adja hozzá a műveletet.  
   ![1. a SharePoint online művelet kép](./media/connectors-create-api-sharepointonline/action-1.png)  
2. Válassza ki a **művelet hozzáadása** hivatkozásra. Ez megnyitja a keresőmezőbe, amelyen kereshetők bármely művelet, szeretné venni. Ebben a példában a SharePoint-műveletek érdekesek lehetnek.    
   ![A SharePoint online művelet kép 2](./media/connectors-create-api-sharepointonline/action-2.png)    
3. Adja meg *sharepoint* SharePoint kapcsolódó tevékenységek keresésére.
4. Válassza ki **SharePoint Online - fájl létrehozása** , a végrehajtandó műveletet.   **Megjegyzés:**: kérni fogja a SharePoint-fiókja eléréséhez, ha nem hozott létre kapcsolatot a SharePoint online szolgáltatásban korábban a logikai alkalmazás engedélyezése.    
   ![A SharePoint online művelet kép 3](./media/connectors-create-api-sharepointonline/action-3.png)    
5. A **fájl létrehozása** megnyílik szabályozhatja.   
   ![A SharePoint online művelet lemezkép 4](./media/connectors-create-api-sharepointonline/action-4.png)     
6. Válassza ki **webhely URL-címe** tallózzon a hely, ahol szeretné létrehozni a fájlt.     
   ![A SharePoint online művelet kép 5](./media/connectors-create-api-sharepointonline/action-5.png)  
7. Válassza ki **mappa elérési útja** , és keresse meg a mappát, ahol az új fájl kerül.  
   ![A SharePoint online művelet kép 6](./media/connectors-create-api-sharepointonline/action-6.png)  
8. Válassza ki a **Fájlnév** vezérlőelemet, és adja meg a létrehozni kívánt fájl nevét. Itt vagy adhatja meg a fájlnevet közvetlenül, vagy használhatja a korábban létrehozott trigger által az tulajdonságok valamelyikét. Ehhez válassza a Tulajdonságok listájából **új elem létrehozásakor kimenetei**. Ez a lista csak megjelenítési kiválasztása után a **Fájlnév** vezérlő. Az ezen walkthough a kiválasztott azonosítója (az új listaelem azonosítója) által létrehozott fájl nevére, a **SharePoint Online - fájl létrehozása** művelet.    
   ![A SharePoint online művelet kép 7](./media/connectors-create-api-sharepointonline/action-7.png)  
9. Válassza ki a **fájl tartalma** vezérlőelemet, és adja meg a tartalmat, amely a létrehozandó fájl lesz írva. A fájl tartalom figyelje meg, használhatja a korábban létrehozott trigger által az tulajdonságok valamelyikét. Egyszerűen válassza a tulajdonságok a megjelenő listában. Másik lehetőségként beírhatja a **fájl tartalma** szöveg közvetlenül a vezérlőelembe. Ebben a példában van kiválasztva az egyes tulajdonságok és szóközöket és a egy kötőjel közötti minden egyes tulajdonság hozzáadva.        
   ![A SharePoint online művelet kép 8](./media/connectors-create-api-sharepointonline/action-8.png)  
10. Mentse a módosításokat a munkafolyamathoz  
11. Gratulálunk, most már rendelkezik egy teljesen működőképes logikai alkalmazást, amely akkor aktiválódik, ha egy új elemet adnak hozzá egy SharePoint Online-listát. Az alkalmazás ezután létrehoz egy fájlt, egyes, az új listaelem tulajdonságai.  Most tesztelheti a SharePoint-lista egy új elem létrehozásával. 

