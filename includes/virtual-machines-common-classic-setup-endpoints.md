
Minden egyes végpont rendelkezik egy *nyilvános port* és egy *magánhálózati port*:

* A nyilvános portot használják az Azure load balancer figyelni a bejövő forgalmat a virtuális gép az internetről.
* A magánhálózati port szolgál a virtuális gép által figyelni a bejövő adatforgalmat, általában egy alkalmazás vagy szolgáltatás fut a virtuális gépen.

Az IP-protokoll és a jól ismert hálózati protokollok kapnak, ha az Azure-portálon végpontok létrehozásához TCP vagy UDP-portok alapértelmezett értékeit. Egyéni végpontok szüksége lesz a megfelelő IP-protokoll (TCP és UDP) és a nyilvános és magánhálózati portot adja meg. Véletlenszerűen szét több virtuális gép bejövő forgalmat, lesz szüksége több végpont már nem álló elosztott terhelésű készlet létrehozásához.

Miután létrehozta a végpont, hozzáférés-vezérlési listaként (ACL) segítségével szabályok meghatározásához, amelyek engedélyezhetnek vagy tagadhatnak meg a végpont a forrás IP-címe alapján a nyilvános portot a bejövő forgalmat. Azonban ha a virtuális gépet egy Azure virtuális hálózatban, használjon hálózati biztonsági csoportok helyette. További információkért lásd: [hálózati biztonsági csoportok](../articles/virtual-network/virtual-networks-nsg.md).

> [!NOTE]
> Az Azure virtuális gépek tűzfal-konfigurálása automatikusan történik, amely automatikusan beállítja Azure távoli kapcsolatot végpontok társított portok. A portok az összes többi végpont a megadott, nem-konfigurálása automatikusan történik a virtuális gép a tűzfalhoz. Amikor létrehoz egy végpontot a virtuális gép, szüksége lesz győződjön meg arról, hogy a tűzfal, a virtuális gép is lehetővé teszi, hogy a protokoll és magánhálózati portot a végpont-konfiguráció megfelelő a forgalmat. A tűzfal konfigurálásáról lásd: a dokumentációját, vagy az operációs rendszer a virtuális gépen futó online súgóját.
>
>

## <a name="create-an-endpoint"></a>Végpont létrehozása
1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **virtuális gépek**, majd kattintson a konfigurálni kívánt virtuális gép nevére.
3. Kattintson a **végpontok** a a **beállítások** csoport. A **végpontok** lap felsorolja a virtuális gép az aktuális végpontjai. (Ez a példa egy Windows virtuális Gépet. A Linux virtuális gép alapértelmezés szerint megjelenik a végpont az SSH.)

   <!-- ![Endpoints](./media/virtual-machines-common-classic-setup-endpoints/endpointswindows.png) -->
   ![Végpontok](./media/virtual-machines-common-classic-setup-endpoints/endpointsblade.png)

4. A végpont bejegyzések fölött a parancssávon kattintson **Hozzáadás**.
5. Az a **végpont hozzáadása** írja be a végpont nevét **neve**.
6. A **protokoll**, válasszon **TCP** vagy **UDP**.
7. A **nyilvános Port**, írja be a port számát, a bejövő forgalom az internetről. A **magánhálózati Port**, írja be a port számát, amelyet a virtuális gép figyel. Ezek a portszámok eltérő lehet. Győződjön meg arról, hogy a tűzfal, a virtuális gép konfigurációja engedélyezi a megfelelő protokollt (a 6. lépés) és magánhálózati portot forgalmat.
10. Kattintson az **OK** gombra.

Az új végpont jelenik meg a **végpontok** lap.

![Végpont létrehozása sikeresen megtörtént](./media/virtual-machines-common-classic-setup-endpoints/endpointcreated.png)

## <a name="manage-the-acl-on-an-endpoint"></a>A végpont ACL kezelése
A számítógépek, amelyek képesek a forgalmat küldeni a kulcstulajdonságokat határozza meg, hogy a végpont ACL korlátozhatja forgalom forrás IP-címe alapján. A lépések végrehajtásával adja hozzá, módosíthatja vagy eltávolíthatja a végpont ACL.

> [!NOTE]
> Ha a végpont egy elosztott terhelésű készlet része, a végpont ACL végzett módosításokat a készlet összes végpontok is vonatkozik.
>
>

Ha a virtuális gépet egy Azure virtuális hálózatban, ajánlott hálózati biztonsági csoportok hozzáférés-vezérlési listák helyett. További információkért lásd: [hálózati biztonsági csoportok](../articles/virtual-network/virtual-networks-nsg.md).

1. Ha még nem tette meg, jelentkezzen be az Azure-portálon.
2. Kattintson a **virtuális gépek**, majd kattintson a konfigurálni kívánt virtuális gép nevére.
3. Kattintson a **Végpontok** elemre. A listában jelölje ki a megfelelő végpont. Az ACL lista van, az oldal alján.

   ![Adja meg a hozzáférés-vezérlési lista adatait](./media/virtual-machines-common-classic-setup-endpoints/aclpreentry.png)

4. Használja a lista sorok hozzáadásához, törléséhez vagy az ACL szabályok szerkesztése, és módosíthatja azok sorrendjét. A **távoli alhálózati** értéke az IP-címtartományok, a bejövő forgalom az internetről, amely az Azure load balancer segítségével engedélyezheti vagy megtagadhatja a forgalmat a forrás IP-címe alapján. Ne adja meg az IP-címtartományt a CIDR-formátumban, más néven a cím előtag formátuma. Példa: `10.1.0.0/8`.

 ![Új ACL-bejegyzéssel](./media/virtual-machines-common-classic-setup-endpoints/newaclentry.png)


Használhatja a szabályokat, az adott számítógépekhez, a számítógépek az interneten lévő megfelelő csak forgalom engedélyezésére, vagy letilthatja a forgalmat a meghatározott, ismert címtartományokból.

A szabályok kiértékelése sorrendben az első szabályhoz kezdési és befejezési az utolsó szabályhoz. Ez azt jelenti, hogy szabályokat kell kell rendelni a legkevésbé korlátozó szigorúbb. További információt és példákat lásd: [Mi az a hálózati hozzáférés-vezérlési lista](../articles/virtual-network/virtual-networks-acl.md).
