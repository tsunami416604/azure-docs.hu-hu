Adjuk hozzá egy eseményindító.

1. Adja meg *sftp* be a keresőmezőbe a logic apps designer válassza ki a **SFTP - amikor egy fájl hozzáadása vagy módosítása** eseményindító   
   ![SFTP eseményindító kép 1](./media/connectors-create-api-sftp/trigger-1.png)  
2. A **amikor egy fájl hozzáadása vagy módosítása esetén** vezérlő megnyílik.  
   ![SFTP eseményindító kép 2](./media/connectors-create-api-sftp/trigger-2.png)  
3. Válassza ki a **...**  a vezérlő jobb oldalán található. Ekkor megnyílik a mappa példányválasztó vezérlő  
   ![SFTP eseményindító kép 3](./media/connectors-create-api-sftp/action-1.png)  
4. Válassza ki a **SFTP** jelölje ki a gyökérmappában található új vagy módosított fájlokat a figyelendő mappát. A legfelső szintű mappa megjelenik a figyelmeztetés a **mappa** vezérlő.  
   ![SFTP eseményindító kép 4](./media/connectors-create-api-sftp/action-2.png)   

A Logic Apps alkalmazást ezen a ponton úgy van konfigurálva, az eseményindítók és műveletek a munkafolyamat futtató akkor kezdődik, amikor egy fájl módosított, vagy az adott SFTP mappában létrehozott eseményindítót. 

> [!NOTE]
> A logikai alkalmazás működéséhez legalább egy eseményindító és egy műveletet kell tartalmaznia. Kövesse a következő szakaszban művelet hozzáadása.  
> 
> 

