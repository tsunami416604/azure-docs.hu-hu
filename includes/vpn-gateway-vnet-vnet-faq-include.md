- A virtuális hálózatok lehetnek azonos vagy eltérő Azure-régiókban (helyeken).

- A felhőszolgáltatás és a terheléselosztási végpont NEM ívelhet át több virtuális hálózaton, akkor sem, ha ezek össze vannak kapcsolva.

- Az Azure Virtual Networkök összekapcsolása nem igényel helyszíni VPN-átjárókat, hacsak nem szükséges a létesítmények közötti kapcsolat.

- A virtuális hálózatok közötti kapcsolat támogatja a virtuális hálózatok csatlakoztatását, azonban nem támogatja a NEM virtuális hálózatban lévő virtuális gépek és felhőszolgáltatások csatlakoztatását.

- A virtuális hálózatok közötti kapcsolathoz útvonalalapú (korábbi nevén dinamikus útválasztású) VPN-típussal rendelkező Azure VPN Gateway átjárók szükségesek. 

- A virtuális hálózati kapcsolat használható egyidejűleg többhelyes VPN-kapcsolatokkal, legfeljebb 10 (alapértelmezett/általános átjárók) vagy 30 (nagy teljesítményű átjárók) VPN-átjáróval olyan virtuális hálózati VPN-átjáró esetében, amely más virtuális hálózatokhoz vagy helyszíni helyekhez csatlakozik.

- A virtuális hálózatok címterei és a helyszíni helyi hálózati helyek nem lehetnek egymással átfedésben. Ha a címterek átfedik egymást, a virtuális hálózatok közötti kapcsolat létrehozása meghiúsul.

- A virtuális hálózatok párjai közötti redundáns alagutak nem támogatottak.

- A virtuális hálózat minden VPN-alagútja az Azure VPN Gateway átjárón elérhető sávszélességet használja, és azonos VPN-átjáró üzemidőre vonatkozó SLA-t az Azure-ban.

- A virtuális hálózatok közötti adatforgalom a Microsoft hálózatán halad át, nem az interneten.

- Az azonos régión belüli virtuális hálózatok közötti adatforgalom ingyenes mindkét irányban. A régiókon átívelő virtuális hálózatok közötti kimenő forgalom díjának megállapítása a virtuális hálózatok közötti kimenő adatátviteli tarifák alapján történik a forrásrégiók alapján. A részletekért tekintse meg a [díjszabást ismertető oldalt](https://azure.microsoft.com/pricing/details/vpn-gateway/).

<!--HONumber=Sep16_HO4-->


