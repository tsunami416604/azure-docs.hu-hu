Miután a tartománynév a rekordjainak propagálása, a böngésző használatával győződjön meg arról, hogy használható-e az egyéni tartománynevet eléréséhez a webalkalmazás az Azure App Service szolgáltatásban kell lennie.

> [!NOTE]
> A CNAME REKORDOT a DNS-rendszerében propagálásához némi időt is igénybe vehet. A szolgáltatás használhatja például a <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> annak ellenőrzéséhez, hogy a CNAME érhető el.
> 
> 

Ha nem már hozzáadta a webes alkalmazás a Traffic Manager-végpontként, ezt névfeloldás működik, az egyéni tartomány nevét útvonalként felvétele a Traffic Manager előtt kell megtennie. A TRAFFIC Manager ezután továbbítja a webalkalmazás. Olvassa el a [telepítése és törlése végpontok](../articles/traffic-manager/traffic-manager-endpoints.md) a webes alkalmazás hozzáadása a Traffic Manager-profil végpontjaként.

> [!NOTE]
> Ha a webalkalmazás nem szerepel a végpont hozzáadásakor, győződjön meg arról, hogy van-e konfigurálva a **szabványos** App Service-csomag mód. Használjon **szabványos** mód a webalkalmazás a Traffic Managerrel működéshez.
> 
> 

1. A böngészőben nyissa meg a [Azure Portal](https://portal.azure.com).
2. Az a **webalkalmazások** lapra, kattintson a nevére, webalkalmazás, jelölje be **beállítások**, majd válassza ki **egyéni tartományok**
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. Az a **egyéni tartományok** panelen kattintson a **állomásnév hozzáadása**.
4. Használja a **állomásnév** beviteli mezők adja meg a webalkalmazás társítja a Traffic Manager szolgáltatásbeli tartománynevére.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)
5. Kattintson a **ellenőrzése** a tartomány neve konfigurációjának mentéséhez.
6. Gomb megnyomásakor **ellenőrzése** Azure lesz indítsa tartományok ellenőrzésének munkafolyamat. Ez ellenőrzi, hogy a tartomány tulajdonosa, valamint állomásnév rendelkezésre állás és a jelentés sikeres vagy az előírásoknak megfelelő guidence a hiba megoldásával kapcsolatos hiba részleteit.    
7. Sikeres érvényesítése után **állomásnév hozzáadása** gomb aktívvá válik, és nem fogja tudni a hozzárendelése állomásnév. Most nyissa meg böngészőben az egyéni tartománynevet. Ekkor megjelenik az alkalmazás fut, az egyéni tartománynév használatával. 
   
   Az egyéni tartománynév konfiguráció befejezése után megjelenik a **tartománynevek** webalkalmazás szakasz.

Ezen a ponton kell lennie a Traffic Manager tartománynevének megadása a böngészőben, és tekintse meg, hogy sikeresen tart, a webes alkalmazást.

