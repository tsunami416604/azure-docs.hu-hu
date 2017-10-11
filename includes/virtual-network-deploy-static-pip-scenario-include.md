## <a name="scenario"></a>Forgatókönyv
Ez a dokumentum részletesen ismerteti a virtuális gép (VM) számára kiosztott statikus nyilvános IP-címet használó központi telepítés keresztül. Ebben a forgatókönyvben egy virtuális saját statikus nyilvános IP-címmel rendelkezik. A virtuális gép nevű alhálózat része **előtér** rendelkezik, ugyanakkor hozzá statikus magánhálózati IP-cím (**192.168.1.101**) alhálózat.

A webkiszolgálók, amelyben az SSL-tanúsítvány csatolva van egy IP-cím SSL-kapcsolatok megkövetelése szükség lehet egy statikus IP-címet. 

![KÉPLEÍRÁS](./media/virtual-network-deploy-static-pip-scenario-include/figure1.png)

A fenti ábrán környezet telepítése az alábbi lépések követésével.

