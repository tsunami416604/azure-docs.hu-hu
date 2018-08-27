Most, hogy hozzáadott egy feltétel, az időt kell végrehajtani valamit érdekes, az eseményindító által generált adatokkal. Hajtsa végre a következő lépésekkel adhatja hozzá a **Salesforce – Get object** művelet. Ez a művelet az adatokat fog kapni minden alkalommal, amikor egy új érdeklődő jön létre. A második műveletet, a Salesforce - Get-objektumot a művelet egy az Office 365-összekötővel e-mailt küldhet az adatokat használó is hozzáadja.  

Ennek konfigurálásához a művelet, szüksége lesz a következő információkat biztosítja. Megfigyelheti, hogy-e az új fájl tulajdonságok némelyike bemenetként az eseményindító által létrehozott könnyen használható adatokat:

| Fájltulajdonság létrehozása | Leírás |
| --- | --- |
| Objektumtípus |Ez az Önt érdeklő Salesforce-objektum típusa. Például a érdeklődő, a fiók, stb. |
| Objektumazonosító |Ez jelöli, hogy az objektum azonosítóját. |

1. Válassza ki **művelet hozzáadása** hivatkozásra. Ez megnyitja a keresőmezőbe, amelyen kereshetők bármely művelet, szeretné venni. Ebben a példában a Salesforce-műveletek érdekesek lehetnek.      
   ![1. a Salesforce művelet kép](./media/connectors-create-api-salesforce/action-1.png)  
2. Adja meg *salesforce* a Salesforce-hoz kapcsolódó tevékenységek keresésére.
3. Válassza ki **Salesforce – Get object** , a végrehajtandó műveletet.   **Megjegyzés:**: a logikai alkalmazás eléréséhez a Salesforce-fiókjához, ha még nem meg korábban engedélyezésére kéri.    
   ![A Salesforce művelet kép 2](./media/connectors-create-api-salesforce/action-2.png)    
4. A **Get object** megnyílik szabályozhatja.  
5. Válassza ki *vezethet* objektum típusaként.
6. Válassza ki a **Objektumazonosító** vezérlő.
7. Válassza ki **...**  bontsa ki a listát a jogkivonatokat, amelyeket bemenetként műveletekhez használható.       
   ![A Salesforce művelet kép 3](./media/connectors-create-api-salesforce/action-3.png)    
8. Válassza ki **érdeklődő azonosítója** megnyílik szabályozhatja.   
   ![A Salesforce művelet lemezkép 4](./media/connectors-create-api-salesforce/action-4.png)     
9. Figyelje meg, hogy a vezető azonosító jogkivonat már az objektum azonosítója vezérlőelem, jelezve, hogy a Get-objektum művelet egy azonosítóval, amely kiváltotta a logikai alkalmazás Érdeklődő érdeklődők azonosítója megegyezik az érdeklődő fogja keresni az.  
   ![A Salesforce művelet kép 5](./media/connectors-create-api-salesforce/action-5.png)  
10. Mentse a munkáját. Ennyi az egész, a Get-objektum művelet hozzáadta a logikai alkalmazáshoz. A Get-objektum vezérlő kell kinéznie:    
    ![A Salesforce művelet kép 6](./media/connectors-create-api-salesforce/action-6.png)  

Most, hogy hozzáadott egy olyan műveletet, az érdeklődő beolvasása, előfordulhat, hogy szeretné valami érdekeset, az újonnan létrehozott érdeklődő együtt. Vállalati érdemes egy e-mail értesíti a terjesztési lista, hogy új érdeklődő létrehozása. Az Office 365-összekötő használatával e-mail küldése az egyes vonatkozó információkat-objektumból az új érdeklődő a Salesforce-ban.  

1. Válassza ki **művelet hozzáadása** verziójúra *e-mail* a keresési vezérlőelemet a. Ezzel úgy szűri a műveleteket, amelyek kapcsolódnak, küldése és fogadása az e-mailt.  
2. Válassza ki a **Office 365 Outlook – e-mail küldése** listaelem. Ha még nem hozott létre egy *kapcsolat* Office 365-fiókjába kéri létrehozza most az Office 365 hitelesítő adatait. Miután elkészült, a **e-mail küldése** megnyílik szabályozhatja.        
   ![A Salesforce művelet kép 7](./media/connectors-create-api-salesforce/action-7.png)  
3. Adja meg az e-mailt a kívánt e-mail-cím a **való** vezérlő.
4. Az a **tulajdonos** vezérlőelemet, adja meg *új érdeklődő létrehozása* – majd válassza ki a *vállalati* token. Ez megjeleníti a *vállalati* mezőt az új érdeklődő létrehozása a Salesforce-ban.  
5. Az a **törzs** vezérlő, az új érdeklődő objektum közül választhat a jogkivonatok bármelyikét, és bármilyen szöveg, amelyet szeretne megjeleníteni az e-mail törzsében is megadhat. Például:  
   ![A Salesforce művelet kép 8](./media/connectors-create-api-salesforce/action-8.png)   
6. Mentse a munkafolyamatot.  

Ennyi az egész. A logikai alkalmazás most már befejeződött.  

Most tesztelheti a logikai alkalmazás: Salesforce-ban, amely megfelel a feltételnek, akkor a létrehozott új érdeklődő létrehozása.  Ha követte a lépéseket ebben az útmutatóban teljesen, ehhez hozzon létre egy érdeklődő tartalmazó e-mail-címmel *amazon.com* benne. Néhány másodperc elteltével a logikai alkalmazás aktiválódik, és az eredmény az alábbihoz hasonló ezt:  
![A Salesforce művelet kép 9](./media/connectors-create-api-salesforce/action-9.png)  

