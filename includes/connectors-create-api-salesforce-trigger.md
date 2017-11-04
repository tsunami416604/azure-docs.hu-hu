Ez az útmutató megtanulhatja, hogyan használható a **Salesforce - amikor létrejön egy objektum** munkafolyamat logic app kezdeményezésének, amikor egy új vezető létrehozza a Salesforce eseményindító.

> [!NOTE]
> Első kérni, hogy jelentkezzen be a Salesforce-fiókhoz, ha még nem hozott egy *kapcsolat* a Salesforce.  
> 
> 

1. Adja meg *salesforce* be a keresőmezőbe a logic apps designer válassza ki a **Salesforce - amikor létrejön egy objektum** eseményindító.  
   ![Salesforce eseményindító kép 1](./media/connectors-create-api-salesforce/trigger-1.png)   
2. A **egy objektumának létrejöttekor** vezérlő jelenik meg.  
   ![Salesforce eseményindító kép 2](./media/connectors-create-api-salesforce/trigger-2.png)   
3. Válassza ki a **objektumtípus** válassza *vezethet* objektumok közül. Ebben a lépésben vannak jelzi, hogy hoz létre egy eseményindítót, amely értesíti a Logic Apps alkalmazást, amikor egy új vezető jön létre a Salesforce-ban.   
   ![Salesforce eseményindító kép 3](./media/connectors-create-api-salesforce/trigger-3.png)   
4. Ennyi az egész. Az eseményindító létrehozott. Azonban meg kell legalább egy művelet annak érdekében, hogy ez egy érvényes logikai alkalmazás létrehozása.    
   ![Salesforce eseményindító kép 4](./media/connectors-create-api-salesforce/trigger-4.png)   

A Logic Apps alkalmazást ezen a ponton úgy van konfigurálva, az eseményindítók és műveletek a munkafolyamat futtató akkor kezdődik, amikor új elem jön létre a Salesforce eseményindítót.  

