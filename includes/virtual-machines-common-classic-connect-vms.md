

![Önálló virtuális gépek a felhőalapú szolgáltatás](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

Ha a virtuális gépeket helyez egy virtuális hálózatot, megadhatja, hogy a használni kívánt hány felhőszolgáltatások betölteni a terheléselosztás és a rendelkezésre állási készletek. Emellett rendezheti a virtuális gépek alhálózatok ugyanúgy a helyszíni hálózat és a virtuális hálózathoz csatlakozni a helyszíni hálózat. Íme egy példa:

![A virtuális hálózatban lévő virtuális gépek](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

Virtuális hálózatok az ajánlott módszer a virtuális gépek Azure-ban való csatlakoztatásához. Az ajánlott eljárás az alkalmazás egyes rétegeinek konfigurálása külön felhőszolgáltatásban. Azonban szükség lehet néhány virtuális gépnél, a másik alkalmazási rétegek egyesítése a azonos felhőszolgáltatás 200 felhőszolgáltatások előfizetésenként legfeljebb belül maradjon. Ezzel és más korlátozások ellenőrzéséhez tekintse meg [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../articles/azure-subscription-service-limits.md).

## <a name="connect-vms-in-a-virtual-network"></a>Csatlakozás a virtuális gépek virtuális hálózatban
A virtuális hálózatban lévő virtuális gépek csatlakozni:

1. A virtuális hálózat létrehozása a [Azure-portálon](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md) , és adja meg a "klasszikus üzembe helyezési".
2. Hozzon létre a felhőalapú szolgáltatások a környezetet, hogy tükrözze a tervező a rendelkezésre állási csoportok és a terheléselosztás. Az Azure portálon kattintson **új > számítási > Felhőszolgáltatás** minden felhőalapú szolgáltatás.

  Mivel kitöltötte a felhőalapú szolgáltatás részleteit, válassza ki ugyanazt _erőforráscsoport_ együtt a virtuális hálózat.

3. Minden új virtuális gép létrehozásához kattintson a **új > számítási**, majd válassza ki a megfelelő Virtuálisgép-lemezkép a **a kiemelt alkalmazások**.

  A virtuális gép **alapjai** panelen válassza ki ugyanazt _erőforráscsoport_ együtt a virtuális hálózat.

  ![Virtuális gép alapvető beállítások panel egy virtuális hálózat használatakor](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_VN.png)

4. Adja meg a virtuális Gépet, **beállítások**, válassza ki a megfelelő _felhőalapú szolgáltatás_ vagy _virtuális hálózati_ a virtuális gép számára.

  Azure fogja kiválasztani az egyéb elem a kijelölés alapján.

  ![Virtuálisgép-beállítások panel egy virtuális hálózat használatakor](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_VN.png)


## <a name="connect-vms-in-a-standalone-cloud-service"></a>Csatlakozás a virtuális gépek önálló felhőszolgáltatásban
Önálló felhőalapú szolgáltatásként lévő virtuális gépek csatlakozni:

1. A felhőszolgáltatás létrehozása a [Azure-portálon](http://portal.azure.com). Kattintson a **új > számítási > Felhőszolgáltatás**. Vagy a felhőalapú szolgáltatás, az üzembe helyezéshez hozhat létre, amikor az első virtuális gép létrehozása.
2. A virtuális gépek létrehozásakor válassza ki a felhőalapú szolgáltatással használt ugyanabban az erőforráscsoportban.

  ![A virtuális gépek hozzáadása egy meglévő felhőszolgáltatáshoz](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_SA.png)

3.  Adja meg az a virtuális gép részleteit, mivel válassza ki az első lépésben létrehozott felhőalapú szolgáltatás nevét.

  ![A virtuális gép egy felhőalapú szolgáltatás kiválasztása](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_SA.png)
