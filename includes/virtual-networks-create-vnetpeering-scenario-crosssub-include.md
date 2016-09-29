## Társviszony létesítés előfizetések között

Ebben a forgatókönyvben társviszonyt fog létesíteni két, eltérő előfizetéshez tartozó virtuális hálózat között.

![előfizetések közötti forgatókönyv](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

A virtuális hálózatok közötti társviszony-létesítés szerepköralapú hozzáférés-vezérlést (RBAC-t) használ a hitelesítéshez. Az eltérő előfizetésekre vonatkozó forgatókönyvben először meg kell adnia a szükséges engedélyeket a társviszony-létesítési hivatkozást létrehozó felhasználóknak:

> [AZURE.NOTE] Ha ugyanaz a felhasználó mindkét előfizetés jogosultságaival rendelkezik, akkor az alábbi 1–4. lépést kihagyhatja.


<!--HONumber=Sep16_HO4-->


