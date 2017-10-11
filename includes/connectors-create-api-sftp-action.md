Most, hogy hozzáadta egy eseményindítót, idejének módosításával az eseményindító által generált adatok érdekes. Kövesse a fenti lépésekkel felvette a a **SFTP - kivonat mappa** művelet. A meghatározott feltételek teljesülése esetén ez a művelet ki a fájl tartalmát. 

Szerint konfigurálhatja ezt a műveletet, akkor adja meg a következő információkat. Megfigyelheti, hogy a rendszer az egyes tulajdonságok az új fájl bemeneteként az eseményindító által létrehozott könnyen használható adatokat:

| SFTP - kivonat mappa tulajdonság | Leírás |
| --- | --- |
| Forrás archív fájl elérési útja |Ez az, hogy a fájl kibontása közben elérési útját. Válassza ki azt a tokenek egy korábbi műveletet, vagy keresse meg a SFTP-kiszolgáló található a fájl elérési útját. |
| A célmappa útvonala |Ez az az elérési utat, ahol kell helyezni a kibontott fájlokat. Válassza ki azt a tokenek egy korábbi művelet, mintha a elérési utat vagy keresse meg a SFTP-kiszolgáló, és válasszon ki egy útvonalat. |
| Felülírja? |Azt jelzi, ha megtalálható-e a kibontott fájllal azonos nevű fájl a célmappa elérési útja, ha a meglévő fájlt, vagy nem írható felül. |

Lássunk neki a fájlok kibontásához, ha a korábban meghatározott feltétel művelettel *igaz*. 

1. Válassza ki **művelet hozzáadása**.        
   ![SFTP művelet feltétel kép 6](./media/connectors-create-api-sftp/condition-6.png)   
2. Válassza ki a **SFTP - kivonat mappa** művelet      
   ![SFTP művelet feltétel kép 7](./media/connectors-create-api-sftp/condition-7.png)   
3. Válassza ki **forrás archív fájl elérési útja**              
   ![SFTP művelet feltétel kép 9](./media/connectors-create-api-sftp/condition-9.png)   
4. Válassza ki a **fájl elérési útját** token. Ez azt jelzi, hogy használni kívánt fájl elérési útját a fájlt, amely az eseményindító található a forrás archív fájl elérési útja.           
   ![SFTP művelet feltétel kép 10](./media/connectors-create-api-sftp/condition-10.png)   
5. Válassza ki **célmappa elérési útja**           
   ![SFTP művelet feltétel kép 11](./media/connectors-create-api-sftp/condition-11.png)   
6. Válassza ki a **fájl elérési útját** token. Ez azt jelzi, hogy használni kívánt fájl elérési útját a fájlt, amely az eseményindító található elérési utat a kibontott fájlok.   
7. Adja meg *\ExtractedFile* a a **a célmappa útvonala** vezérlő. Ehhez a fájl elérési útja lexikális elem szerepel a célként megadott mappa elérési útja vezérlő után.         
   ![SFTP művelet feltétel kép 12](./media/connectors-create-api-sftp/condition-12.png)   
8. Adja meg *igaz* a a **felülírása?* vezérlő annak jelzésére, hogy a meglévő fájlok felül, ha azok a néven a kibontott fájlokat kell-e.      
   ![SFTP művelet feltétel kép 13](./media/connectors-create-api-sftp/condition-13.png)   
9. Mentse a módosításokat a munkafolyamat  

