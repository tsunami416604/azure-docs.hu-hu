Ebben a példában I bemutatja, hogyan használható a **SharePoint online-hoz - egy új elem létrehozásakor** eseményindító logic app munkafolyamat elindítására, egy új elem létrehozásakor a SharePoint Online listáját.

> [!NOTE]
> Első kérni, hogy jelentkezzen be a SharePoint-fiókba, ha még nem hozott egy *kapcsolat* SharePoint online-hoz.  
> 
> 

1. Adja meg *sharepoint* be a keresőmezőbe a logic apps designer válassza ki a **SharePoint online-hoz - egy új elem létrehozásakor** eseményindító.  
   ![SharePoint online eseményindító kép](./media/connectors-create-api-sharepointonline/trigger-1.png)  
2. A **új elem létrehozásakor** vezérlő jelenik meg.  
   ![SharePoint online eseményindító kép 2](./media/connectors-create-api-sharepointonline/trigger-2.png)   
3. Válassza ki a **webhely URL-címe**. Ez az új elemek indítani a figyelni kívánt SharePoint online webhelyen.  
   ![SharePoint online eseményindító kép 3](./media/connectors-create-api-sharepointonline/trigger-3.png)   
4. Válassza ki a **neve**. A rendszer a figyelni kívánt új elemek, amelyek kiváltják a munkafolyamatot a SharePoint Online webhelyen.  
   ![SharePoint online eseményindító kép 4](./media/connectors-create-api-sharepointonline/trigger-4.png)   

A Logic Apps alkalmazást ezen a ponton úgy van konfigurálva, az eseményindító, amely akkor kezdődik, eseményindítók és műveletek a munkafolyamat futását. Ez akkor kerül sor egy új elem létrehozásakor kijelölt SharePoint Online listában.  

