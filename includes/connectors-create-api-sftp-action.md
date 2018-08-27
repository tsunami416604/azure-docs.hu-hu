Most, hogy hozzáadott egy eseményindítót, valamit a idő érdekes, az eseményindító által generált adatokkal. Hajtsa végre a következő lépésekkel adhatja hozzá egy a **SFTP - kivonat mappa** művelet. Ez a művelet fog bontsa ki a fájl tartalmát, a meghatározott feltételek teljesülése esetén. 

Ennek konfigurálásához a művelet, szüksége lesz a következő információkat biztosítja. Megfigyelheti, hogy-e az új fájl tulajdonságok némelyike bemenetként az eseményindító által létrehozott könnyen használható adatokat:

| SFTP - kivonat mappatulajdonság | Leírás |
| --- | --- |
| Forrásarchívum fájlelérési útja |Ez az az elérési utat a fájl kibontása közben. Válassza ki a tokenek valamelyikének a korábbi műveletet, vagy tallózással keresse meg az SFTP-kiszolgáló található a fájl elérési útját. |
| Célmappa elérési útja |Ez az az elérési utat, ahol a kibontott fájlok kerülnek. Válassza ki azt a tokenek egy korábbi művelet az elérési úthoz, vagy tallózással keresse meg az SFTP-kiszolgáló, és válassza ki az elérési utat. |
| Felülírás? |Azt jelzi, ha egy fájl neve megegyezik a kicsomagolt fájl megtalálható a célmappa elérési útja, ha a meglévő fájlt, vagy nem írható felül. |

Első lépések a fájlok kibontásához, ha a korábban meghatározott feltétel művelettel *igaz*. 

1. Válassza ki **művelet hozzáadása**.        
   ![Az SFTP művelet feltétel kép 6](./media/connectors-create-api-sftp/condition-6.png)   
2. Válassza ki a **SFTP - kivonat mappa** művelet      
   ![Az SFTP művelet feltétel kép 7](./media/connectors-create-api-sftp/condition-7.png)   
3. Válassza ki **forrásarchívum fájlelérési útja**              
   ![Az SFTP művelet feltétel kép 9](./media/connectors-create-api-sftp/condition-9.png)   
4. Válassza ki a **fájl elérési útja** token. Ez azt jelzi, hogy Ön fogja használni, amely az eseményindító található a fájl elérési útja a forrásarchívum fájlelérési útja.           
   ![Az SFTP művelet feltétel kép 10](./media/connectors-create-api-sftp/condition-10.png)   
5. Válassza ki **célmappa elérési útja**           
   ![Az SFTP művelet feltétel kép 11](./media/connectors-create-api-sftp/condition-11.png)   
6. Válassza ki a **fájl elérési útja** token. Ez azt jelzi, hogy használhatja, amely az eseményindító található a fájl elérési útja az elérési úthoz, a a kibontott fájlokat.   
7. Adja meg *\ExtractedFile* a a **célmappa elérési útja** vezérlő. Ezt a fájlt a cél-mappa elérési útja vezérlőelem elérésiút-token után.         
   ![Az SFTP művelet feltétel kép 12](./media/connectors-create-api-sftp/condition-12.png)   
8. Adja meg *igaz* az a **felülírás?* vezérlő jelzi, hogy meglévő fájlok felülírja-e, ha ugyanazzal a névvel rendelkeznek, a kibontott fájlokat.      
   ![Az SFTP művelet feltétel kép 13](./media/connectors-create-api-sftp/condition-13.png)   
9. Mentse a módosításokat a munkafolyamathoz  

