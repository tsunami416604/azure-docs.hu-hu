## <a name="use-the-azure-portal"></a>Az Azure Portal használata
1. Válassza ki a virtuális Gépet kíván telepíteni, majd válassza ki a *újratelepíteni* gombra a *beállítások* panelen. Görgessen le lásd szeretne a **támogatási és hibaelhárítási** szakaszt, amely tartalmazza az "Újra" gombra az alábbi példában látható módon:
   
    ![Az Azure virtuális gép panel](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. A művelet megerősítését, válassza ki a *újratelepíteni* gombra:
   
    ![Telepítse újra a virtuális gép panel](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. A **állapot** változik a virtuális gép *Frissítéskísérleti* , a virtuális gép felkészül, hogy telepítse újra, a következő példában látható módon:
   
    ![Virtuális gép frissítése](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. A **állapot** átvált *indítása* , a virtuális gép elindul egy új Azure gazdagépen az alábbi példában látható módon:
   
    ![Virtuális gép indítása](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. A virtuális Gépet a rendszerindítási folyamat befejezését követően a **állapot** adja vissza *futtató*, jelezve a virtuális gép rendelkezik lett újratelepítése sikeresen megtörtént:
   
    ![Virtuális gép fut](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

