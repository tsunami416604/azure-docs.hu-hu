## Biztonsági mentési házirend meghatározása

A biztonsági mentési házirend egy mátrixot határoz meg ahhoz, hogy mikor készüljön pillanatkép az adatokról, és mennyi ideig őrizze meg a rendszer a pillanatképeket. Virtuális gépek biztonsági mentéséről szóló házirend meghatározása esetén *naponta egyszer* indíthat el biztonsági mentési feladatot. Az újonnan létrehozott házirendek a tárolón érvényesülnek. A biztonsági mentési házirendek felülete a következőképpen néz ki:

![Biztonsági mentési házirend](./media/backup-create-policy-for-vms/backup-policy.png)

Házirend létrehozása:

1. Adjon meg egy nevet a **Házirend neve** mezőben.

2. Az adatokról napi vagy heti rendszerességgel készíthet pillanatképet. A **Biztonsági mentés gyakorisága** legördülő menüben kiválaszthatja, hogy napi vagy heti rendszerességgel szeretne pillanatképet készíteni az adatokról.

    - Ha a napi rendszerességet választja, a kiemelt vezérlővel kiválaszthatja, hogy a nap melyik szakában szeretné elkészíteni a pillanatképet. Ha módosítani szeretné a kiválasztott órát, törölje a kijelölését, majd válasszon ki egy másikat.

    ![Napi biztonsági mentési házirend](./media/backup-create-policy-for-vms/backup-policy-daily.png) <br/>

    - Ha a heti rendszerességet választja, a kiemelt vezérlőkkel kiválaszthatja, hogy a hét melyik napjain, illetve a nap melyik szakában szeretné elkészíteni a pillanatképet. A nap menüben válasszon ki egy vagy több napot. Az óra menüben válasszon ki egy órát. Ha módosítani szeretné a kiválasztott órát, törölje a kijelölését, majd válasszon ki egy másikat.

    ![Heti biztonsági mentési házirend](./media/backup-create-policy-for-vms/backup-policy-weekly.png)

3. Alapértelmezés szerint az összes **Megőrzési időtartam** beállítás ki van választva. Törölje a megőrzési időtartam azon korlátozásainak kijelölését, amelyeket nem szeretne használni. Ezután adja meg használni kívánt időtartamo(ka)t.

    A havi és éves megőrzési időtartamok lehetővé teszik a heti vagy napi növekményes pillanatképek megadását.

    >[AZURE.NOTE] A virtuális gépek védelméhez a rendszer naponta egyszer készít biztonsági mentést. A biztonsági mentés futtatásának ideje minden megőrzési időtartam esetén ugyanaz.

4. A házirend beállításainak megadása után kattintson a panel tetején található **Mentés** gombra.

    Ekkor a rendszer automatikusan alkalmazza az új házirendet a tárolón.


<!--HONumber=Sep16_HO4-->


