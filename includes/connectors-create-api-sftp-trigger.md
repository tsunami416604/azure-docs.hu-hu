Adjunk hozzá egy eseményindító.

1. Adja meg *sftp* a keresőmezőbe írja be a logic apps Designerben kattintson a **SFTP - amikor egy fájl hozzáadásakor és módosításakor** eseményindító   
   ![Az SFTP-eseményindító lemezkép 1](./media/connectors-create-api-sftp/trigger-1.png)  
2. A **amikor felvesznek vagy módosítanak egy fájlt** vezérlő nyit meg  
   ![Az SFTP-eseményindító kép 2](./media/connectors-create-api-sftp/trigger-2.png)  
3. Válassza ki a **...**  a vezérlő jobb oldalán található. Ekkor megnyílik a mappa Dátumválasztó vezérlőelem  
   ![Az SFTP-eseményindító kép 3](./media/connectors-create-api-sftp/action-1.png)  
4. Válassza ki a **SFTP** jelölje ki a mappa figyelése új vagy módosított fájl a gyökérmappában található. A legfelső szintű mappa megjelenik a figyelmeztetés a **mappa** vezérlő.  
   ![Az SFTP-eseményindító lemezkép 4](./media/connectors-create-api-sftp/action-2.png)   

Ezen a ponton a logikai alkalmazás egy eseményindítóval, amely más eseményindítók és műveletek a munkafolyamat futtatását akkor kezdődik, amikor egy fájlt módosító vagy az adott SFTP-mappába van konfigurálva. 

> [!NOTE]
> A logikai alkalmazás megfelelő működéséhez tartalmaznia kell legalább egy triggert és a egy műveletet. Kövesse a következő szakaszban vegyen fel egy műveletet.  
> 
> 

