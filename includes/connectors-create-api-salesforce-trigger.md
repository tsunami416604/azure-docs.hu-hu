Ebben az útmutatóban, megtudhatja, hogyan használható a **Salesforce - objektum létrehozásakor** eseményindítót egy logikai alkalmazás munkafolyamatának kezdeményezni, ha új érdeklődő jön létre a Salesforce-ban.

> [!NOTE]
> Meg fogja kérni a Salesforce-fiókjához bejelentkezni, ha még nem hozott egy *kapcsolat* a Salesforce-hoz.  
> 
> 

1. Adja meg *salesforce* a keresőmezőbe írja be a logic apps Designerben kattintson a **Salesforce - objektum létrehozásakor** eseményindító.  
   ![1. a Salesforce eseményindító kép](./media/connectors-create-api-salesforce/trigger-1.png)   
2. A **objektum létrehozásakor** vezérlőelem jelenik meg.  
   ![Salesforce-eseményindító kép 2](./media/connectors-create-api-salesforce/trigger-2.png)   
3. Válassza ki a **objektumtípus** majd *vezethet* objektumok listájáról. Ebben a lépésben is jelzi, hogy hoz létre egy eseményindítót, amely a logikai alkalmazás értesíteni fogjuk, ha új érdeklődő jön létre a Salesforce-ban.   
   ![Salesforce-eseményindító kép 3](./media/connectors-create-api-salesforce/trigger-3.png)   
4. Ennyi az egész. Az eseményindító létrehozott. Azonban szüksége, hozzon létre legalább egy műveletet, és győződjön meg arról, ez egy érvényes logikai alkalmazást.    
   ![Salesforce-eseményindító lemezkép 4](./media/connectors-create-api-salesforce/trigger-4.png)   

Ezen a ponton a logikai alkalmazás egy eseményindítóval, amely más eseményindítók és műveletek a munkafolyamat futtatását akkor kezdődik, amikor új elem jön létre a Salesforce-ban van konfigurálva.  

