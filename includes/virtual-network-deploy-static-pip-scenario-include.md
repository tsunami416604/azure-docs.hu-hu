## <a name="scenario"></a>Forgatókönyv
Ez a dokumentum egy statikus nyilvános IP-cím virtuális géphez (VM) használó központi telepítések vezeti végig. Ebben a forgatókönyvben egyetlen virtuális gép saját statikus nyilvános IP-címmel rendelkezik. A virtuális gép nevű alhálózat része **előtérbeli** és a egy statikus magánhálózati IP-cím is rendelkezik (**192.168.1.101**) az alhálózat.

A webkiszolgálók, amelyben az SSL-tanúsítvány csatolva van egy IP-cím SSL-kapcsolatot igénylő szükség lehet egy statikus IP-címet. 

![KÉPLEÍRÁS](./media/virtual-network-deploy-static-pip-scenario-include/figure1.png)

Követheti a fenti ábrán látható környezet üzembe helyezéséhez az alábbi lépéseket.

