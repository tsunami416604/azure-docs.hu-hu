Ebben a példában bemutatom majd, hogyan használható a **a SharePoint online-ban – Ha új elem jön létre** kezdeményezése a logikaialkalmazás-munkafolyamat egy SharePoint Online-lista egy új elem létrehozásakor eseményindító.

> [!NOTE]
> Meg fogja kérni a bejelentkezés a SharePoint-fiókjába, ha még nem hozott egy *kapcsolat* SharePoint online-hoz.  
> 
> 

1. Adja meg *sharepoint* a keresőmezőbe írja be a logic apps Designerben kattintson a **a SharePoint online-ban – Ha új elem jön létre** eseményindító.  
   ![A SharePoint online eseményindító kép ](./media/connectors-create-api-sharepointonline/trigger-1.png)  
2. A **új elem létrehozásakor** vezérlőelem jelenik meg.  
   ![A SharePoint online eseményindító kép 2](./media/connectors-create-api-sharepointonline/trigger-2.png)   
3. Válassza ki a **webhely URL-címe**. Ez az a SharePoint online webhelyen, szeretné figyelni a munkafolyamatot kiváltó új elemeket.  
   ![A SharePoint online eseményindító kép 3](./media/connectors-create-api-sharepointonline/trigger-3.png)   
4. Válassza ki a **listanév**. A rendszer a figyelni kívánt új elemeket, amelyek kiváltják a munkafolyamatot a SharePoint Online webhelyen.  
   ![A SharePoint online eseményindító lemezkép 4](./media/connectors-create-api-sharepointonline/trigger-4.png)   

Ezen a ponton a logikai alkalmazás egy eseményindítóval, amely más eseményindítók és műveletek a munkafolyamat futtatását elkezdi van konfigurálva. Ez történik minden alkalommal, amikor új elem jön létre a kiválasztott SharePoint Online-listát.  

