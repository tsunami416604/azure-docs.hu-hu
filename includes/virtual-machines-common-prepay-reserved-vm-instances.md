# <a name="prepay-for-virtual-machines-with-reserved-vm-instances"></a>A virtuális gépek fenntartott virtuális gép osztályt előre fizetés

A virtuális gépek előre fizetés léptékű és költségtakarékosabb munkavégzésben fenntartott virtuális gép osztályt. További információkért lásd: [fenntartott virtuálisgép-példányok ajánlat](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Megvásárolhatja fenntartott virtuálisgép-példánya a [Azure-portálon](https://portal.azure.com). Vásárolhat egy fenntartott virtuális gép példány:
-   Legalább egy vállalati vagy használatalapú előfizetés tulajdonosának szerepkört kell lennie.
-   A vállalati előfizetések foglalás vásárlás engedélyezni kell a [EA portal](https://ea.azure.com).

## <a name="buy-a-reserved-virtual-machine-instance"></a>A fenntartott virtuális gépet példánya megvásárlása
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** > **foglalások**.
3. Válassza ki **Hozzáadás** új foglalás megvásárlásához.
4. Töltse ki a kötelező mezőket. Virtuális gép a kiválasztott attribútumok csak akkor lekérni a Foglalás kedvezményeket megfelelő példányok. A Virtuálisgép-példányok, amelyek beszerzése a kedvezményeket tényleges száma attól függ, a hatókör és a kiválasztott mennyiség.

    | Mező      | Leírás|
    |:------------|:--------------|
    |Name (Név)        |Ezt a fenntartást neve.| 
    |Előfizetés|Az előfizetés, a Foglalás fizeti. A fizetési módot, az előfizetés fel van töltve a Foglalás társaságuk költségeit. Az előfizetés típusúnak kell lennie egy nagyvállalati szerződés (ajánlat száma: MS-AZR - 0017P) vagy használatalapú fizetés (ajánlat száma: MS-AZR - 0003P). A nagyvállalati előfizetéssel a költségek a beléptetési pénzügyi kötelezettségvállalást a egyenleg vonni vagy mint túlhasználati számítjuk fel. Használatalapú fizetés előfizetés a költségek számlázása a hitelkártya vagy számla fizetési módot, az előfizetésben való.|    
    |Hatókör       |A fenntartási hatókör egy előfizetés vagy több előfizetés (megosztott hatókör) is foglalkozik. Ha: <ul><li>Egyetlen előfizetés - a Foglalás kedvezményeket ehhez az előfizetéshez alkalmazása a virtuális gépekhez. </li><li>Megosztott - a Foglalás kedvezményeket vonatkozik az előfizetése a számlázási környezeten belül futó virtuális gépek. A vállalati ügyfelek a megosztott hatókör, a regisztráció és előfizetéseket (kivéve a fejlesztési és tesztelési célú előfizetések) belül a beléptetési magában foglalja. Használatalapú fizetés ügyfelek esetén a megosztott hatóköre hozta létre a fiókadminisztrátor előfizetéseket használatalapú fizetésre.</li></ul>|
    |Hely    |Az Azure-régió, hogy a Foglalás mutatja.|    
    |Virtuális gép mérete     |A Virtuálisgép-példány mérete.|
    |Időtartam        |Az egy vagy három év.|
    |Mennyiség    |A megvásárolt belül a Foglalás példányok száma. A mennyiség futó kérheti le a számlázási kedvezményeket Virtuálisgép-példányok száma. Például ha 10 D2 virtuális gépek USA keleti régiója fut, majd volna meg mennyiség minden futó gépek juttatása maximalizálása 10. |
5. Megtekintheti a fenntartás költsége kiválasztásakor **költség**.

    ![Képernyőfelvétel a Foglalás beszerzési elküldése előtt](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Válassza ki **beszerzési**.
7. Válassza ki **foglalás megtekintése** vásárlását állapotát tekintheti meg.

    ![Képernyőfelvétel a Foglalás beszerzési elküldése előtt](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>További lépések 
A foglalási kedvezményeket automatikusan alkalmazzák a futó virtuális gépek, amelyek megfelelnek a fenntartási hatókör és attribútumok számát. Frissítheti a Foglalás keresztül hatóköre [Azure-portálon](https://portal.azure.com), PowerShell, a parancssori felületen vagy az API-n keresztül. 

A foglalási kezelése című témakörben talál [kezelése Azure fenntartott virtuálisgép-példányok](../articles/billing/billing-manage-reserved-vm-instance.md).

Tudhat meg többet a fenntartott virtuálisgép-példányok, tekintse meg a következő cikkekben talál.

- [Kevesebbet költeni a virtuális gépek a fenntartott virtuálisgép-példányok](../articles/billing/billing-save-compute-costs-reservations.md)
- [A fenntartott virtuális gép példány kedvezményeket alkalmazásának a módját ismertetése](../articles/billing/billing-understand-vm-reservation-charges.md)
- [A használatalapú fizetéses előfizetésre fenntartott példány használatának megértéséhez](../articles/billing/billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés használata fenntartott példány ismertetése](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [A Windows szoftverek költségeit fenntartott példányok nem találhatók](../articles/billing/billing-reserved-instance-windows-software-costs.md)