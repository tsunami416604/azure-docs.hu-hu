# <a name="support-matrix"></a>Támogatási mátrix

| | **VMware – Azure** |**Hyper-V – Azure**|**Azure – Azure**|**Hyper-V másodlagos helyre**|**VMware másodlagos helyre**
--|--|--|--|--|--
Támogatott esetek |Igen|Igen|Nem|Igen*|Nem
Támogatott verzió | vCenter 6.5, 6.0 vagy 5.5| Windows Server 2016-ot, a Windows Server 2012 R2 | NA |Windows Server 2016-ot, a Windows Server 2012 R2|NA
Támogatott konfigurációval|vCenter, ESXi| Hyper-V fürt, a Hyper-V gazdagép|NA|Hyper-V fürt, a Hyper-V gazdagép|NA|
Az Azure Site helyreállítási telepítési Planner példányát futtató / is csatolást kiszolgálók száma |Egyetlen (egyszerre egy vCenter-kiszolgáló vagy egy ESXi-kiszolgálón tartozó virtuális gépek kell csatolást.)|Több (virtuális gépek több gazdagépek vagy-gazdagépfürtök lehet profil egyszerre)| NA |Több (virtuális gépek több gazdagépek vagy-gazdagépfürtök lehet profil egyszerre)| NA

* Az eszközt elsősorban az Azure katasztrófa utáni helyreállítása a Hyper-V van. A Hyper-V másodlagos hely katasztrófa utáni helyreállításra használható csak az ügyféloldali javaslatok megértése forrás például az szükséges hálózati sávszélesség, a szükséges szabad tárhely a forrás Hyper-V kiszolgálók és a számok és kötegelt kötegelés kezdeti replikálás definíciók.  Hagyja figyelmen kívül az Azure javaslatok és a költségeket a jelentésből. A Get átviteli műveletet is nem alkalmazható másodlagos hely katasztrófa utáni helyreállítása a Hyper-v.
