Most, hogy hozzáadta a feltétel, az idő kell végrehajtani valamit az eseményindító által generált adatok érdekes. Kövesse a fenti lépésekkel felvette a **Salesforce - Get objektum** művelet. Ez a művelet egy új vezető létrehozásakor kap az adatokat. A második műveletet a Salesforce - objektum művelet Get az Office 365-összekötővel e-mailt küldhet a adatait használó is hozzáadhat.  

Szerint konfigurálhatja ezt a műveletet, akkor adja meg a következő információkat. Megfigyelheti, hogy a rendszer az egyes tulajdonságok az új fájl bemeneteként az eseményindító által létrehozott könnyen használható adatokat:

| Fájl tulajdonság létrehozása | Leírás |
| --- | --- |
| Objektumtípus |Ez a Salesforce-objektum érdekli típusa. Többek között az érdeklődési, a fiók, stb. |
| objektum azonosítója |Ez az objektum azonosítóját jelenti. |

1. Válassza ki **művelet hozzáadása** hivatkozásra. Ez a keresési mezőbe, ahol kereshet bármilyen műveletet meg szeretné igénybe megnyílik. Ebben a példában a Salesforce-műveletek végezhetők, iránt.      
   ![Salesforce művelet kép 1](./media/connectors-create-api-salesforce/action-1.png)  
2. Adja meg *salesforce* salesforce kapcsolatos műveletek kereséséhez.
3. Válassza ki **Salesforce - Get objektum** , a végrehajtandó műveletet.   **Megjegyzés:**: kérni fogja a Logic Apps alkalmazást a Salesforce-fiókhoz elérésére, ha még nem meg korábban engedélyezésére.    
   ![Salesforce művelet kép 2](./media/connectors-create-api-salesforce/action-2.png)    
4. A **Get objektum** megnyílik szabályozzák.  
5. Válassza ki *vezethet* objektum típusaként.
6. Válassza ki a **Objektumazonosító** vezérlő.
7. Válassza ki **...**  tokenek műveletekhez használható bemenetként a lista kibontásához.       
   ![Salesforce művelet kép 3](./media/connectors-create-api-salesforce/action-3.png)    
8. Válassza ki **vezethet azonosító** megnyílik szabályozzák.   
   ![Salesforce művelet kép 4](./media/connectors-create-api-salesforce/action-4.png)     
9. Láthatja, hogy a vezethet azonosító token most a Objektumazonosító vezérlőben, jelezve, hogy a Get-objektum művelet az ID, amely a logikai alkalmazás kiváltó átfutási átfutási azonosítója megegyezik egy vezető fogja keresni.  
   ![Salesforce művelet kép 5](./media/connectors-create-api-salesforce/action-5.png)  
10. Mentse a munkáját. Ennyi az egész, a Get-objektum művelet hozzáadta a Logic Apps alkalmazást. A Get-objektum vezérlő kell kinéznie:    
    ![Salesforce művelet kép 6](./media/connectors-create-api-salesforce/action-6.png)  

Most, hogy egy vezető beolvasandó művelet jelentek meg, érdemes lehet az újonnan létrehozott átfutási az érdekes foglalkozhat. Vállalati érdemes lehet értesíteni egy terjesztési listát, hogy egy új vezető létrejött-e e-mailt küldeni. Az Office 365-összekötő segítségével egy részét a szükséges adatokat e-mailt küldeni az új átfutási objektum a Salesforce-ban.  

1. Válassza ki **művelet hozzáadása** írja be *e-mail* vezérlőjében. A szűrés a műveleteket, az alábbiakhoz kapcsolódó mailek küldésekor és fogadásakor.  
2. Válassza ki a **Office 365 Outlook - egy e-mailek küldése** listaelemet. Ha még nem hozott létre egy *kapcsolat* Office 365-fiókjához kérni fogja az Office 365 hitelesítő most létrehozták. Miután elkészült, a **egy e-mailek küldése** megnyílik szabályozzák.        
   ![Salesforce művelet kép 7](./media/connectors-create-api-salesforce/action-7.png)  
3. Adja meg az e-mail cím, amelyet az e-mail üzenetek küldéséhez a **való** vezérlő.
4. Az a **tulajdonos** szabályozása, adja meg *létrehozott új vezethet* – adja meg, majd a *vállalati* token. Ez megjeleníti a *vállalati* a Salesforce létrehozott új vezető a mező.  
5. Az a **törzs** vezérlő, a jogkivonatok bármelyikét kiválaszthatja az új átfutási objektumot, és más szeretné az e-mail törzsében megjelenítendő szöveget is megadhat. Íme egy példa:  
   ![Salesforce művelet kép 8](./media/connectors-create-api-salesforce/action-8.png)   
6. A munkafolyamat mentése.  

Ennyi az egész. A Logic Apps alkalmazást befejeződött.  

Most, tesztelheti a Logic Apps alkalmazást: Salesforce, hozzon létre egy új vezető létrehozott feltételt.  Ha ez az útmutató teljesen követi, készítsen egy vezető tartalmazó e-mail címmel rendelkező *amazon.com* azt. Néhány másodpercen belül a Logic Apps alkalmazást váltódik ki, és az eredmény az alábbihoz hasonló ezt:  
![Salesforce művelet kép 9](./media/connectors-create-api-salesforce/action-9.png)  

