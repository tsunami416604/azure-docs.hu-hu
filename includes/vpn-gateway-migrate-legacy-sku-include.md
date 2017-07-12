> [!NOTE]
> A VPN-átjáró nyilvános IP-címe módosul a régiről az új termékváltozatra való migrálás során.
> 

Az Azure VPN-átjárók nem méretezhetők át közvetlenül a régi és az új termékváltozatok között. Ha olyan VPN-átjárókkal rendelkezik a Resource Manager üzemi modelljében, amelyek a termékváltozatok régebbi verzióját használják, azokat migrálhatja az új termékváltozatokra. A migráláshoz törölnie kell a virtuális hálózat meglévő VPN-átjáróját, majd létre kell hoznia egy újat.

A migrálási munkafolyamat:

1. Távolítson el minden, a virtuális hálózat átjárójához tartozó kapcsolatot.
2. Törölje a régi VPN-átjárót.
3. Hozza létre az új VPN-átjárót.
4. Frissítse a helyszíni VPN-eszközt az új VPN-átjáró IP-címével (a helyek közötti kapcsolatokhoz).
5. Frissítse minden olyan helyi virtuális hálózatok közötti kapcsolat átjárója IP-címének értékét, amely ehhez az átjáróhoz kapcsolódik.
6. Töltse le az új VPN-konfigurációs csomagokat a virtuális hálózathoz ezen a VPN-átjárón keresztül kapcsolódó P2S-ügyfelekhez.
7. Hozza létre újra a virtuális hálózat átjárójához tartozó kapcsolatokat.