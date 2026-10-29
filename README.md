# Infraestrutura de Redes

**Responsável técnica:** Alexandra Carneiro (Técnica de Redes e Segurança)

Planeamento e configuração de uma infraestrutura de rede local: bastidor e hardware, segmentação por VLANs, comutação, encaminhamento inter-VLAN, NAT e licenciamento de servidor. Inclui uma proposta completa para dois escritórios e as topologias em Cisco Packet Tracer.

Os cenários usam empresas fictícias (POST e ALGARFIVESTAR) e nomes de teste.

## Bastidor

Planeamento de um bastidor de telecomunicações para uma empresa com 9 postos de trabalho ligados a um switch central, com um servidor dedicado na sala da arrecadação.

| Equipamento | Modelo | Detalhe |
| --- | --- | --- |
| Bastidor 18U | RACK-18UFN-8D | 19", 988 x 600 x 800 mm (altura x largura x profundidade), aço SPCC preto, IP20, CE e ROHS, até 300 kg; porta frontal em vidro temperado com fechadura, porta traseira em metal, painéis laterais amovíveis; 4 rodas e 4 pés niveladores ajustáveis; unidade de ventilação de teto com 4 ventoinhas |
| Servidor | HPE ProLiant DL360 Gen11 | 1U, 12 núcleos; controlador de domínio (Active Directory e DNS), DHCP, servidor de ficheiros (ficheiros partilhados com acesso restrito) e backups automáticos; 2 fontes de alimentação hot-swap de 1000 W, discos SSD, até 2 CPUs, 8 TB de RAM DDR5 e 4 portas Gigabit LAN integradas |
| Switch | Ubiquiti UniFi USW-24-POE Gen2 | 24 portas, 16 PoE+ (92 W de potência partilhada), sem ventoinha; 14 portas livres e 2 slots SFP para fibra; alimenta as câmaras de videovigilância por PoE+, sem fontes externas |
| Patch panel | Lanberg PPS6-1024-B | 24 portas Cat6, 1U, metal; 15 portas livres; fixado logo acima do switch e ligado a ele por patch cords Cat6 a Gigabit |
| UPS | APC Smart-UPS SMT2200RMI2UC | 2200 VA, 1980 W de saída real, onda sinusoidal pura, bateria selada VRLA, 2U, 683 mm de profundidade, 42,3 kg; 9 saídas IEC, ecrã LCD, monitorização remota (SmartConnect), baterias hot-swap e 5 anos de vida útil da bateria |
| Gestão de cabos | Painel Lanberg AK-1201-B | 1U, com anéis metálicos |
| Cabos | Patch cords Ewent Cat6 UTP 0,5 m | Cobre puro, Gigabit a 1000 Mbit/s, compatíveis com PoE; o comprimento de 0,5 m é o exato para não deixar folga pendurada |

- A profundidade de 800 mm é a mínima para servidores e UPS de rack profissionais (os bastidores de parede de 450 mm ou 600 mm não chegam). Os equipamentos iniciais ocupam cerca de 8U a 9U, por isso ficam mais de 45% livres para crescer.
- A carga inicial estimada da UPS é de 370 W, menos de 20% da capacidade de 1980 W, com mais de 1600 W de margem.

### Disposição física (de cima para baixo)

| U | Equipamento | Motivo da posição |
| --- | --- | --- |
| 18 | Patch panel | Topo, junto à entrada dos cabos |
| 17 | Painel organizador de cabos | Arrumação dos cabos de interligação |
| 16 | Switch | Ligação curta ao patch panel |
| 15 | 1U livre | Ventilação |
| 14 | Servidor | Centro do bastidor |
| 11-13 | 3U livres | Isolamento térmico entre o servidor e a UPS |
| 1-3 | UPS (2U + 1U) | Base: com 42,3 kg, é o centro de gravidade ideal e impede o tombamento |

- A unidade de ventilação de teto, com 4 ventoinhas, extrai o ar quente para cima. Os espaços vazios impedem a propagação de calor entre a UPS e o servidor.

### Proteção da sala da arrecadação

- Câmara IP de alta definição no teto, apontada à porta frontal do bastidor, alimentada por PoE+; as gravações vão para um volume dedicado e seguro no servidor de ficheiros HPE, com retenção de 30 dias e alertas de movimento em tempo real para os telemóveis da equipa de TI fora de horas.
- Controlo de acessos por cartões de proximidade RFID ou códigos numéricos individuais (substitui a fechadura mecânica), com registo digital auditável de cada abertura (identidade, data e hora). O bastidor fica fechado em todas as faces (dupla autenticação física).
- Ar condicionado dedicado a funcionar em contínuo, com a temperatura ambiente entre 19 e 21 °C.
- Extintor de CO2 de 5 kg junto à entrada da sala (sem agentes condutores ou corrosivos, sem resíduos e seguro para eletrónica ligada).

## Bastidor 2.0

Evolução do bastidor anterior: junta fibra ótica, router/firewall e ventilação ativa, com ligação por fibra a um bastidor secundário no exterior do edifício.

| U | Equipamento | Função |
| --- | --- | --- |
| 18 | Bandeja de ventilação | Extração de ar quente e monitorização da temperatura |
| 17 | ODF / Patch panel de fibra | Receção do cabo de fibra que vem do exterior |
| 16 | Patch panel RJ45 | Organização da cablagem de cobre |
| 15 | Organizador de cabos | Arrumação dos cabos de interligação |
| 14 | Switch Ubiquiti USW-24-POE | Distribuição local e alimentação das câmaras |
| 13 | Router / Firewall UDM-Pro | Controlo e segurança de acesso à internet |
| 12 | Espaço livre | Isolamento térmico e circulação de ar |
| 11 | Servidor HPE ProLiant Gen11 | Centralização dos serviços críticos |
| 3-10 | 8U livres | Expansão futura e entrada de ar fresco |
| 1-2 | UPS APC Smart-UPS 2200VA | Base do bastidor |

- A distribuição ocupa 10U dos 18U (44% livres). A profundidade de 800 mm também salvaguarda o raio de curvatura seguro dos cabos de fibra ótica.
- ODF Lanberg FOP1-24SS-LDB, com acopladores LC Duplex (Bematik LC/PC multimodo OM3), tampa frontal amovível, cassete central para o excesso de cabo e fixações traseiras contra o esticamento; 1U, com capacidade até 24 adaptadores. Recebe o cabo de fibra do exterior e protege as fusões internas (trabalho especializado de fusão de fibra na instalação).
- Router / Firewall Ubiquiti UniFi Dream Machine Pro (UDM-Pro), 1U, tudo-em-um: processador quad-core a 1,7 GHz, controlador de rede, portas 10G SFP+ e baía para disco de 3,5" para armazenamento local de videovigilância; regras de firewall avançadas e prevenção de intrusões (IPS/IDS). As portas Ethernet integradas não fornecem PoE.
- Bandeja de ventilação Lanberg AK-1503-B, 1U, 322 mm de profundidade, com sensor de temperatura, 4 ventoinhas de alto desempenho com termostato programável e ecrã LCD; 76 W com as ventoinhas ativas e espaço traseiro livre para a subida do ar quente.
- A UPS alimenta o servidor, o switch, o router de segurança e o arrefecimento ativo; a carga inicial é de 479 W (24% da capacidade).
- O switch tem 12 portas livres (contra 14 na versão anterior) e 2 portas SFP para uma ligação direta por fibra ao bastidor exterior. Decisão: sem conversores de média externos (o switch já tem portas de fibra integradas; a fibra direta elimina equipamentos extra, consumo e pontos de falha).
- O servidor tem as mesmas funções (domínio, DNS, DHCP, ficheiros e backups) e arquiva de forma centralizada as gravações de videovigilância (sala técnica e exterior).

## Rede POST: VLANs e endereçamento

Projeto e implementação da rede local para as novas instalações da empresa POST, em Cisco Packet Tracer: 17 computadores, 2 impressoras de rede, 1 servidor central, 8 câmaras IP, 1 router e 2 switches de 24 portas.

### Planta e inventário

- Sala de reuniões no canto superior esquerdo; gabinetes 1, 2 e 3 em sequência na ala esquerda; gabinete 4 (Direção) na zona sul; receção à entrada, ligada ao espaço de circulação; Espaço de Trabalho 1 no centro; Espaço de Trabalho 2 no canto superior direito; uma pequena sala técnica junto ao Espaço de Trabalho 2, para o bastidor e o servidor.
- Inventário: 17 computadores de secretária (1 em cada um dos 4 gabinetes, 1 na receção, 8 no Espaço de Trabalho 1 e 4 no Espaço de Trabalho 2), 2 impressoras de rede (em zonas comuns), 1 servidor central, 8 câmaras IP (3 exteriores, 4 interiores e mais 1 no interior da sala técnica, a monitorizar o bastidor), 1 router e 2 switches de 24 portas.
- O sistema de segurança original tinha 2 painéis centrais de comando, detetores de intrusão e 7 câmaras IP.

### VLANs e endereçamento IP

- Endereçamento privado de classe C, com o ID da VLAN no 3.º octeto e máscara /24 (255.255.255.0). O primeiro endereço útil de cada gama fica reservado para o gateway (subinterface do router). Gama útil por VLAN: .2 a .254.

| VLAN | Zona | Sub-rede | Gateway |
| --- | --- | --- | --- |
| 10 | Gabinetes | 192.168.10.0/24 | 192.168.10.1 |
| 20 | Espaços de Trabalho | 192.168.20.0/24 | 192.168.20.1 |
| 30 | Receção | 192.168.30.0/24 | 192.168.30.1 |
| 40 | Sala Técnica (servidor) | 192.168.40.0/24 | 192.168.40.1 |
| 50 | Impressoras | 192.168.50.0/24 | 192.168.50.1 |
| 60 | Segurança (CCTV e painéis) | 192.168.60.0/24 | 192.168.60.1 |

- Segmentação por VLANs para isolar dados administrativos (VLAN 10) e operacionais (VLAN 20); a receção (VLAN 30) tem restrições por ser zona pública; servidor (VLAN 40) e impressoras (VLAN 50) ficam em sub-redes isoladas para travar movimentos laterais; videovigilância e segurança concentradas na VLAN 60.

### Mapa de portas

| Switch | Porta | Ligação | Tipo | VLAN |
| --- | --- | --- | --- | --- |
| SW1 | Gi0/1 | Router R1 Gi0/0 | Trunk, cobre direto | Todas |
| SW1 | Fa0/23 - Fa0/24 | SW2 Fa0/23 - Fa0/24 | Trunk (EtherChannel), cobre cruzado | Todas |
| SW1 | Fa0/1 - Fa0/12 | Computadores do Espaço de Trabalho | Acesso, cobre direto | 20 |
| SW1 | Fa0/13 | Servidor principal | Acesso, cobre direto | 40 |
| SW1 | Fa0/14 - Fa0/15 | Impressoras de rede | Acesso, cobre direto | 50 |
| SW2 | Fa0/1 - Fa0/4 | Computadores dos gabinetes | Acesso, cobre direto | 10 |
| SW2 | Fa0/5 | Computador da receção | Acesso, cobre direto | 30 |
| SW2 | Fa0/6 - Fa0/13 | Câmaras IP 1 a 8 | Acesso, cobre direto | 60 |

### Configuração base

- Segurança base no R1, SW1 e SW2: nome de anfitrião, resolução de nomes por DNS desativada, proteção do modo privilegiado, segurança da linha de consola e tempo limite de sessão.
- VLANs criadas na base de dados dos switches, portas de acesso associadas à VLAN de cada posto e spanning-tree portfast para convergência rápida.

### EtherChannel e trunk

- Para maximizar a largura de banda e dar tolerância a falhas na ligação entre os dois switches, as interfaces físicas foram agrupadas num único canal lógico através do protocolo dinâmico LACP. Depois, o canal foi definido em modo trunk para transportar todas as VLANs.

```
SW1(config)#interface port-channel 1
SW1(config-if)#description LIGACAO_LOGICA_ETHERCHANNEL
SW1(config-if)#switchport mode trunk
SW1(config-if)#switchport trunk allowed vlan 10,20,30,40,50,60

SW1(config)#interface range fastEthernet 0/23-24
SW1(config-if-range)#description BACKBONE_PARA_SW2
SW1(config-if-range)#switchport mode trunk
SW1(config-if-range)#channel-group 1 mode active
```

<img src="imagens/etherchannel.png" width="700" alt="Configuração EtherChannel no switch SW2">

*EtherChannel no SW2: interface port-channel 1, modo trunk, com as VLANs 10, 20, 30, 40, 50 e 60 permitidas.*

### Encaminhamento inter-VLAN

- Router-on-a-stick no R1: a porta GigabitEthernet0/0 foi dividida em subinterfaces lógicas com encapsulamento dot1Q (IEEE 802.1Q), uma por VLAN, com o endereço IP do gateway respetivo: G0/0.10 (192.168.10.1), G0/0.20 (192.168.20.1), G0/0.30 (192.168.30.1), G0/0.40 (192.168.40.1), G0/0.50 (192.168.50.1) e G0/0.60 (192.168.60.1).
- Ligação trunk do SW1 ao R1 ativada, para o router receber o tráfego marcado de todas as VLANs.

### Port-security

- Para evitar que pessoas não autorizadas liguem portáteis estranhos às tomadas dos gabinetes ou dos espaços de trabalho, foi ativada a segurança de porta. Se for detetado um endereço MAC desconhecido, a porta desliga-se automaticamente (shutdown).

```
SW1(config)#interface range fastEthernet 0/1-15
SW1(config-if-range)#switchport mode access
SW1(config-if-range)#switchport port-security
SW1(config-if-range)#switchport port-security maximum 1
SW1(config-if-range)#switchport port-security mac-address sticky
SW1(config-if-range)#switchport port-security violation shutdown

SW2(config)#interface range fastEthernet 0/1-13
SW2(config-if-range)#switchport mode access
SW2(config-if-range)#switchport port-security
SW2(config-if-range)#switchport port-security maximum 1
SW2(config-if-range)#switchport port-security mac-address sticky
SW2(config-if-range)#switchport port-security violation shutdown
```

### Listas de controlo de acesso (ACLs)

- A segurança entre os setores da POST foi centralizada no router R1, com regras estritas:
  - A receção ficou totalmente isolada, para impedir que qualquer utilizador externo na zona pública aceda aos computadores da administração, da produção ou ao servidor central. A única exceção aberta foi para a rede de impressoras, para o operador do balcão conseguir imprimir os documentos de trabalho do dia a dia.
  - A rede de videovigilância ficou completamente blindada: os funcionários comuns dos gabinetes e da área operacional não têm permissão para monitorizar ou aceder ao sistema de segurança.
  - Apenas o servidor principal, instalado na sala técnica, tem autorização para comunicar livremente com as câmaras IP, para capturar e guardar todas as gravações do circuito fechado de TV. Os restantes computadores da empresa (gabinetes e produção) continuam a comunicar normalmente entre si e com o servidor.

```
R1(config)#no access-list 110
R1(config)#access-list 110 deny ip 192.168.10.0 0.0.0.255 192.168.60.0 0.0.0.255
R1(config)#access-list 110 permit ip any any
R1(config)#interface gigabitEthernet 0/0.10
R1(config-subif)#ip access-group 110 in

R1(config)#no access-list 160
R1(config)#access-list 160 deny ip 192.168.20.0 0.0.0.255 192.168.60.0 0.0.0.255
R1(config)#access-list 160 permit ip any any
R1(config)#interface gigabitEthernet 0/0.20
R1(config-subif)#ip access-group 160 in
```

<img src="imagens/acls.png" width="700" alt="ACLs no router R1">

*ACLs no R1: a lista 110 bloqueia a VLAN 10 (Gabinetes) para a VLAN 60 (Segurança) e a lista 160 bloqueia a VLAN 20 (Espaços de Trabalho) para a VLAN 60, aplicadas nas subinterfaces G0/0.10 e G0/0.20.*

### Análise crítica

- Pontos fortes: segmentação rigorosa por VLANs, port-security, EtherChannel (alta disponibilidade e duplicação da largura de banda do backbone) e administração lógica centralizada.
- Pontos fracos: o router-on-a-stick faz do R1 um ponto único de falha (se falhar ou perder energia, todo o encaminhamento inter-VLAN para, e a empresa perde a comunicação interna e o acesso ao servidor); movimento lateral pelas impressoras (a receção pode aceder às impressoras e as impressoras comunicam com o resto da rede, por isso um atacante que infete o posto da receção pode usar uma impressora como ponte para chegar ao servidor principal); não há rede sem fios isolada para visitantes (a receção depende só do cabo); esforço elevado de administração manual à medida que se adicionam novos equipamentos.
- Melhorias propostas: rede Wi-Fi corporativa com pontos de acesso geridos de forma centralizada e duas VLANs isoladas (interna com WPA3 para os funcionários e convidados com portal de autenticação); proteção da sala técnica (fecho eletrónico por cartão RFID ou biométrico na porta, ar condicionado dedicado a manter 18 a 21 °C, UPS dedicada no bastidor para desligamento seguro e câmara IP a monitorizar o interior e a entrada da sala técnica).

### Testes de validação (ping)

- Receção - Impressoras, Receção - Servidor e Espaços de Trabalho, Receção - Gabinete, Espaços de Trabalho / Gabinete / Receção - Câmara, Gabinete - Servidor, Câmaras - Servidor e Impressoras - Servidor.
- Os resultados confirmam que o R1 controla e filtra a passagem de dados entre VLANs como planeado.

## Switch: VLANs e port-security

Configuração de dois switches (2960-24TT) com segmentação por departamento:

| VLAN | Nome | Uso | Sub-rede |
| --- | --- | --- | --- |
| 10 | RECECAO | Atendimento inicial | 192.168.10.0/24 |
| 20 | FORMACAO | Sala de formação | 192.168.20.0/24 |
| 30 | RH | Recursos Humanos | 192.168.30.0/24 |
| 40 | SERVIDOR | Serviços críticos da organização | 192.168.40.0/24 (servidor com IP fixo 192.168.40.10) |

<img src="imagens/topologia-switch-vlans.png" width="700" alt="Topologia com dois switches e quatro VLANs">

*Topologia: Switch1 e Switch2 (2960-24TT) com as VLANs 10 (Receção), 20 (Formação), 30 (RH) e 40 (Servidor), e os endereços IP dos postos.*

### Segurança base

- Resolução de nomes por DNS desativada (`no ip domain-lookup`) e palavra-passe cifrada no modo privilegiado (`enable secret`).

### Criação das VLANs (nos dois switches)

```
SW1(config)#vlan 10
SW1(config-vlan)# name RECECAO
SW1(config-vlan)#vlan 20
SW1(config-vlan)# name FORMACAO
SW1(config-vlan)#vlan 30
SW1(config-vlan)# name RH
SW1(config-vlan)#vlan 40
SW1(config-vlan)# name SERVIDOR
SW1(config-vlan)#exit
```

- As mesmas quatro VLANs foram criadas no Switch2, com os mesmos nomes.

### Configuração das interfaces

- As interfaces ligadas aos dispositivos finais foram configuradas em modo de acesso (`switchport mode access`) e associadas às suas VLANs. Para acelerar a convergência da rede, foi ativado o spanning-tree portfast nas portas do Switch2. A proteção das portas foi reforçada com port-security, a limitar cada porta a apenas um endereço MAC aprendido de forma dinâmica (sticky) e a bloquear de imediato a interface (`violation shutdown`) em caso de tentativa de acesso não autorizado.

```
SW2(config)#interface range fa0/1 - 2
SW2(config-if-range)#switchport mode access
SW2(config-if-range)#switchport access vlan 10
SW2(config-if-range)#spanning-tree portfast
SW2(config-if-range)#switchport port-security
SW2(config-if-range)#switchport port-security maximum 1
SW2(config-if-range)#switchport port-security mac-address sticky
SW2(config-if-range)#switchport port-security violation shutdown

SW2(config)#interface range fa0/3 - 6
SW2(config-if-range)#switchport mode access
SW2(config-if-range)#switchport access vlan 20
SW2(config-if-range)#spanning-tree portfast
SW2(config-if-range)#switchport port-security
SW2(config-if-range)#switchport port-security maximum 1
SW2(config-if-range)#switchport port-security mac-address sticky
SW2(config-if-range)#switchport port-security violation shutdown

SW1(config)#interface range fa0/1 - 3
SW1(config-if-range)#switchport mode access
SW1(config-if-range)#switchport access vlan 30
SW1(config-if-range)#switchport port-security
SW1(config-if-range)#switchport port-security maximum 1
SW1(config-if-range)#switchport port-security mac-address sticky
SW1(config-if-range)#switchport port-security violation shutdown

SW1(config)#interface fa0/10
SW1(config-if)#switchport mode access
SW1(config-if)#switchport access vlan 40
SW1(config-if)#switchport port-security
SW1(config-if)#switchport port-security maximum 1
SW1(config-if)#switchport port-security mac-address sticky
SW1(config-if)#switchport port-security violation shutdown
```

<img src="imagens/portfast-port-security.png" width="600" alt="Portfast e port-security no switch SW2">

*Portfast e port-security no SW2 (portas Fa0/3 a Fa0/6, VLAN 20): máximo de 1 MAC sticky por porta e bloqueio da interface em caso de violação.*

### Backbone EtherChannel

- Para interligar os switches, foi configurada uma agregação de links (LACP/EtherChannel) nas interfaces fa0/23 e fa0/24. Esta configuração estabelece um canal lógico de alta disponibilidade e redundância, a operar em modo trunk para permitir o tráfego segregado de todas as VLANs entre os equipamentos.

```
SW1(config)#interface range fa0/23 - 24
SW1(config-if-range)# description BACKBONE_PARA_SW2
SW1(config-if-range)# switchport mode trunk
SW1(config-if-range)#channel-group 1 mode active

SW1(config)#interface port-channel 1
SW1(config-if)# description BACKBONE_fuso_SW2
SW1(config-if)# switchport mode trunk
SW1(config-if)# switchport trunk allowed vlan 10,20,30,40
```

### Desativar portas não utilizadas

- Como medida de boas práticas em segurança de redes, todas as interfaces sem dispositivos ligados foram desativadas administrativamente (shutdown), para mitigar vetores de ataque internos.

```
SW1(config)#interface range fa0/4 - 9 , fa0/11 - 22
SW1(config-if-range)#shutdown

SW2(config)#interface range fa0/7 - 22
SW2(config-if-range)#shutdown
```

### Proteger o acesso pela consola

- A porta de consola foi protegida com autenticação e um tempo de expiração de sessão (`exec-timeout 5 0`, ou seja, 5 minutos sem atividade), para prevenir acessos físicos indevidos. A configuração foi guardada com `write memory` e `copy run start`.

```
SW1(config)#line console 0
SW1(config-line)#login
SW1(config-line)#exec-timeout 5 0
```

### Testes de validação (ping)

- Comunicação por ICMP confirmada dentro da mesma VLAN (VLAN 10, VLAN 20 e VLAN 30) e isolamento completo entre departamentos diferentes.

## Router e NAT

Configuração do router (empresa ALGARFIVESTAR) em Cisco Packet Tracer: encaminhamento inter-VLAN e acesso à internet por NAT.

### Ligações físicas

| Origem | Destino | Tipo de cabo |
| --- | --- | --- |
| R1 Gig0/0 | SW1 Gig0/1 | Cobre direto |
| R1 Gig0/1 | ISP Gig0/1 | Cobre direto |
| SW1 Fa0/23 | Switch1 Fa0/23 | Cobre direto |
| SW1 Fa0/24 | Switch1 Fa0/24 | Cobre direto |

- A porta Gig0/1 do SW1 (ligação ao R1) está em modo trunk.

### Configuração inicial do R1

- Nome de anfitrião R1, resolução de nomes por DNS desativada (`no ip domain-lookup`), palavra-passe de modo privilegiado (`enable secret`) e encriptação de todas as palavras-passe guardadas (`service password-encryption`).
- A porta G0/0 foi ativada com `no shutdown` e marcada como zona interna de confiança com `ip nat inside`.

### Subinterfaces do router - gateways das VLANs

- Criação de subinterfaces lógicas na porta GigabitEthernet 0/0 para implementar o encaminhamento inter-VLAN. Para cada subinterface, foi aplicado o encapsulamento IEEE 802.1Q (dot1Q) correspondente ao ID de cada VLAN e atribuído o endereço IP respetivo, que passa a atuar como gateway padrão para os dispositivos.

```
R1(config)#int g0/0.10
R1(config-subif)#encapsulation dot1Q 10
R1(config-subif)#ip address 192.168.10.1 255.255.255.0
R1(config-subif)#description GATEWAY_VLAN10

R1(config)#int g0/0.20
R1(config-subif)#encapsulation dot1Q 20
R1(config-subif)#ip address 192.168.20.1 255.255.255.0
R1(config-subif)#description GATEWAY_VLAN20

R1(config)#int g0/0.30
R1(config-subif)#encapsulation dot1Q 30
R1(config-subif)#ip address 192.168.30.1 255.255.255.0
R1(config-subif)#description GATEWAY_VLAN30

R1(config)#int g0/0.40
R1(config-subif)#encapsulation dot1Q 40
R1(config-subif)#ip address 192.168.40.1 255.255.255.0
R1(config-subif)#description GATEWAY_VLAN40
```

### Tabela de endereços IP

| Dispositivo | Interface | Endereço IP | Máscara | Gateway |
| --- | --- | --- | --- | --- |
| Router0 | Gig0/1 | 200.0.0.2 | 255.255.255.252 (/30) | 200.0.0.1 |
| ISP | Gig0/1 | 200.0.0.1 | 255.255.255.252 (/30) | - |
| Portátil Rec.1 | Fa0 | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| Portátil Rec.2 | Fa0 | 192.168.10.11 | 255.255.255.0 | 192.168.10.1 |
| PC Form 1 | Fa0 | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 |
| PC Form 2 | Fa0 | 192.168.20.11 | 255.255.255.0 | 192.168.20.1 |
| PC Form 3 | Fa0 | 192.168.20.13 | 255.255.255.0 | 192.168.20.1 |
| PC Form 4 | Fa0 | 192.168.20.12 | 255.255.255.0 | 192.168.20.1 |
| PC RH 1 | Fa0 | 192.168.30.10 | 255.255.255.0 | 192.168.30.1 |
| PC RH 2 | Fa0 | 192.168.30.11 | 255.255.255.0 | 192.168.30.1 |
| PC RH 3 | Fa0 | 192.168.30.12 | 255.255.255.0 | 192.168.30.1 |
| Servidor | Fa0 | 192.168.40.10 | 255.255.255.0 | 192.168.40.1 |

- Os endereços IP dos dispositivos foram alterados para evitar conflitos com o IP do router.

### Ligação à operadora (ISP)

- A porta G0/1 do R1 tem o endereço público 200.0.0.2/30 e está marcada como saída para a WAN com `ip nat outside`.
- Router da operadora (ISP): nome de anfitrião ISP, resolução de nomes por DNS desativada, palavra-passe de modo privilegiado e endereço 200.0.0.1 na interface GigabitEthernet 0/1, ativada com `no shutdown` para comunicar com a empresa.
- A interface G0/0 do router ISP, por não ser utilizada, foi desativada com `shutdown` e marcada com a descrição `UNUSED_PORT`, por questões de segurança e boas práticas.

<img src="imagens/router-isp.png" width="700" alt="Configuração do router da operadora (ISP)">

*Router da operadora (ISP): nome, palavra-passe cifrada no modo privilegiado e endereço 200.0.0.1/30 na interface G0/1.*

### Rota por omissão e NAT

- Para permitir que a rede interna aceda a redes externas (internet), foi configurada uma rota padrão a apontar para o router da operadora. O serviço de NAT foi ativado com a funcionalidade "Overload" (PAT), para todos os endereços privados da empresa serem traduzidos para o endereço público da interface Gig0/1 na saída para a operadora.

```
R1(config)#ip route 0.0.0.0 0.0.0.0 200.0.0.1

R1(config)#access-list 1 permit 192.168.10.0 0.0.0.255
R1(config)#access-list 1 permit 192.168.20.0 0.0.0.255
R1(config)#access-list 1 permit 192.168.30.0 0.0.0.255
R1(config)#access-list 1 permit 192.168.40.0 0.0.0.255

R1(config)#ip nat inside source list 1 interface g0/1 overload
```

### Testes de validação (ping)

- Comunicação por ICMP com sucesso dentro da mesma VLAN e entre VLANs (acesso ao servidor e entre departamentos); os indicadores de ligação aparecem a verde na topologia.

### Exemplo de sub-rede

- Rede 192.168.1.0/24 (classe C) com um servidor (192.168.1.1), três salas e 12 postos (Sala 1: 192.168.1.10 a 192.168.1.13; Sala 2: 192.168.1.20 a 192.168.1.23; Sala 3: 192.168.1.30 a 192.168.1.33).

<img src="imagens/topologia-sub-rede.png" width="700" alt="Topologia da rede 192.168.1.0/24 com três salas">

*Rede 192.168.1.0/24: servidor 192.168.1.1 e três salas com 4 postos cada.*

## Licenciamento Windows Server

Modelo de licenciamento para um servidor físico com Windows Server 2022 (empresa POST), com Active Directory, DNS, DHCP, servidor de ficheiros e servidor de impressão.

- Hardware: 2 processadores físicos com 10 núcleos cada, 20 núcleos no total; 2 máquinas virtuais no arranque, com previsão de mais 2 (4 no total); 16 postos de trabalho e 16 utilizadores.
- Edição recomendada: Windows Server 2022 Standard (até 2 máquinas virtuais com o licenciamento completo dos núcleos; para 4 máquinas virtuais, é preciso licenciar de novo os 20 núcleos). A edição Datacenter (virtualização ilimitada) não foi a escolhida para este cenário.
- Núcleos a licenciar: 20 (mínimo de 8 por processador e 16 por servidor cumpridos). As licenças de núcleo vendem-se em pacotes de 2 ou de 16. Para a expansão para 4 máquinas virtuais, mais 20 licenças de núcleo, 40 no total.
- CALs: o modelo é "Core + CAL" — a licença do servidor só dá o direito de instalar o sistema; cada utilizador ou dispositivo precisa de uma CAL (User CAL ou Device CAL). A Device CAL é a recomendada, porque a relação é de 1 utilizador para 1 computador e a licença fica com o computador físico, o que protege o investimento com entradas e saídas de funcionários, turnos ou trabalho a tempo parcial. São precisas exatamente 16 Device CALs.
- Remote Desktop Services: as CALs normais não cobrem as sessões remotas, por isso são precisas CALs de RDS adicionais — 16 RDS Device CALs.

## Proposta: dois escritórios

Proposta técnica para a rede de um edifício de escritórios em dois espaços: o escritório principal no rés-do-chão (cerca de 340 m²) e o escritório secundário no 12.º andar (cerca de 240 m²). Âmbito: rede local de dados, voz sobre IP e videovigilância em circuito fechado.

### Arquitetura da rede e equipamentos ativos

- A interligação entre o escritório do rés-do-chão e o do 12.º andar é feita pela coluna técnica existente no edifício. Para eliminar qualquer ponto único de falha na ligação vertical e garantir alta disponibilidade, foi instalado um backbone redundante em fibra ótica multimodo OM4 entre a arrecadação técnica principal e a secundária. A infraestrutura usa duas ligações físicas em paralelo, configuradas em modo de agregação de tráfego (LACP / EtherChannel) por portas SFP+, com redundância instantânea em caso de falha de um dos cabos e duplicação da capacidade de transmissão entre pisos para 20 Gbps, imunes a interferências eletromagnéticas.
- Rés-do-chão: a arrecadação técnica acolhe um bastidor de chão de 18U/22U com dois switches geríveis de 24 portas Gigabit PoE+. 12.º andar: a arrecadação técnica conta com um bastidor de parede compacto de 12U, equipado com um switch gerível de 24 portas Gigabit PoE+ para a ilha central de trabalho e os equipamentos locais.
- A rede é segmentada logicamente através de VLANs dedicadas, para otimizar o desempenho e a segurança.
- O centro da infraestrutura conta com um servidor físico em formato rack instalado no bastidor do rés-do-chão, equipado com fontes de alimentação redundantes e armazenamento RAID. O sistema operativo escolhido é o Windows Server 2022 Standard, acompanhado por 22 licenças de acesso por dispositivo (Device CALs) para cobrir todos os postos de trabalho da infraestrutura.

### Videovigilância (CCTV)

- Sistema de videovigilância IP com 12 câmaras de alta definição com visão noturna por infravermelhos e alimentação direta pelo cabo de rede (PoE). No rés-do-chão, 8 câmaras a cobrir o perímetro exterior, os acessos principais, as zonas de circulação, as áreas de trabalho abertas e o interior da arrecadação técnica. No 12.º andar, 4 câmaras para o controlo de entrada no piso, a ilha central de trabalho, o espaço aberto e a arrecadação do bastidor secundário.
- As imagens são geridas e armazenadas de forma contínua num gravador NVR de 16 canais instalado no bastidor principal do rés-do-chão, equipado com um disco rígido dedicado de 6 TB. O dimensionamento do armazenamento cumpre as diretrizes da Comissão Nacional de Proteção de Dados (CNPD) e o RGPD, que recomendam um período máximo de conservação de gravações de 30 dias em edifícios corporativos. Com a compressão H.265+, a gravação contínua das 12 câmaras de 4 MP gera uma ocupação diária de cerca de 180 GB, por isso o disco de 6 TB dá capacidade para mais de 30 dias de retenção, com o NVR programado para a sobreposição e eliminação automática do histórico ao atingir o limite regulamentar dos 30 dias.

### Conformidade com as restrições do edifício

- A solução é 100% ligada por cabo, sem qualquer ponto de acesso ou infraestrutura sem fios (Wi-Fi).
- A instalação respeita integralmente a estrutura existente, sem abertura de roços, perfuração de elementos estruturais ou demolição de paredes. Toda a passagem dos cabos de rede de categoria 6 e da fibra ótica usa os tetos falsos modulares e as calhas técnicas de superfície já presentes no edifício.
- A proposta inclui a instalação completa da cablagem e das tomadas de rede RJ45 para todos os postos de trabalho do edifício. No fornecimento de equipamentos terminais, são apenas as impressoras de rede e os telefones VoIP solicitados; ficam excluídos os computadores de secretária e os dispositivos móveis.

### Cablagem

- Cabo UTP Categoria 6 (Cat6), escolhido por responder aos requisitos de velocidade e largura de banda da organização, com transmissões estáveis a 1 Gbps e capacidade para 10 Gbps no percurso instalado. A flexibilidade e o diâmetro adequado permitem respeitar os raios de curvatura de segurança e rentabilizar o espaço útil disponível de 10 centímetros acima do teto falso e nas calhas técnicas do imóvel.
- A quantidade de cabo foi calculada a medir o percurso real de cada ligação, a partir do patch panel no interior da arrecadação técnica de cada piso até ao ponto final de tomada. Cada ligação inclui a subida do bastidor ao teto falso, o percurso horizontal no teto falso, a descida de parede até à tomada e uma sobra técnica total de 3 metros (1,5 metros em cada extremidade) para manuseamentos e manutenções futuras.
- Rés-do-chão: 34 pontos de rede (16 computadores, 1 servidor, 6 telefones VoIP, 3 impressoras e 8 câmaras CCTV), com cerca de 680 metros de cabo. 12.º andar: 12 pontos de rede (ilha central, telefone, impressora e câmaras), com cerca de 180 metros de cabo. Total geral de 860 metros de cabo UTP Cat6, o que exige 3 caixas de cabo de 305 metros cada, com margem de segurança para a obra.
- Backbone em fibra ótica: dois lances de cabo de fibra ótica multimodo OM4 passados pela coluna técnica do prédio. Com a altura de 36 metros entre pisos e os percursos horizontais até aos bastidores, com margem para fusões nas cassetes ODF, cada lance tem 60 metros. Total de 120 metros de fibra ótica OM4, com redundância física total e agregação LACP a 20 Gbps.
- Calhas técnicas de superfície em PVC, aplicadas apenas nas zonas expostas, nas descidas verticais ao longo das paredes desde o teto falso até à altura das tomadas dos postos de trabalho e dos pontos de videovigilância. Cerca de 25 descidas de parede, com uma altura média de 1,80 metros cada, num total de 45 metros de calha técnica de superfície.

### Equipamento incluído

- Bastidor de chão 19" 18U/22U (800 mm de profundidade); bastidor de parede 19" 12U (53 x 40 x 60 cm); 3 switches geríveis Ubiquiti USW-24-POE (24 portas PoE+); gateway / router de segurança Ubiquiti UDM-Pro; servidor rack 1U HPE ProLiant Gen11 (20 núcleos); UPS APC Smart-UPS 2200VA Rack 2U (bastidor principal do rés-do-chão); UPS Salicru SPS 750 Advance R2 Rack 1U (bastidor do 12.º andar); painel de ventilação ativa 1U Lanberg com termostato (rés-do-chão).
- Material passivo: 3 caixas de cabo UTP Categoria 6 (bobina de 305 metros); 2 lances de cabo de fibra ótica multimodo OM4 (120 metros); 25 calhas técnicas de superfície em PVC (2 metros cada); 24 tomadas salientes RJ45 duplas Categoria 6 de parede; 2 sacos de conectores RJ45 Categoria 6 macho (saco de 50 unidades); 3 patch panels RJ45 de 24 portas Categoria 6 em formato 1U; 2 ODF / patch panels de fibra ótica 1U com adaptadores LC; 3 painéis organizadores de cabos 1U com anéis metálicos; 50 cabos de interligação (patch cords Cat6 de 0,5 m); 50 cabos de interligação (patch cords Cat6 de 3 m).
- Videovigilância e terminais: 1 gravador NVR IP TP-Link VIGI de 16 câmaras (NVR1016H); 1 disco rígido WD Purple 3,5" de 6 TB SATA3 (WD64PURZ); 12 câmaras IP 4 MP PoE Hikvision HiLook (IPC-T240HA-LU); 7 telefones VoIP Yealink SIP-T31P; 4 impressoras de rede multifunções Brother MFC-L2800DW.
- Licenciamento: Windows Server 2022 Standard, licença de 16 núcleos (1 unidade) e pacote de 2 núcleos (2 unidades), ou seja, 20 núcleos; Device CALs em pacote de 5 (4 unidades) e Device CAL individual (2 unidades), ou seja, 22 Device CALs.
- Serviços: instalação física, passagem de cabos e montagem de bastidores; configuração lógica de rede (VLANs, router, switches, NVR e servidor); certificação da cablagem estruturada e emissão de relatórios.

### Esquema lógico (VLANs e endereçamento IP)

| Identificação | VLAN | Sub-rede IP | Máscara de rede | Gateway predefinido |
| --- | --- | --- | --- | --- |
| Gestão exclusiva de ativos (switches / UDM / NVR) | 99 | 192.168.99.0 | 255.255.255.0 | 192.168.99.1 |
| Gabinetes | 10 | 192.168.10.0 | 255.255.255.0 | 192.168.10.1 |
| Espaços Trabalho | 20 | 192.168.20.0 | 255.255.255.0 | 192.168.20.1 |
| Receção | 30 | 192.168.30.0 | 255.255.255.0 | 192.168.30.1 |
| Servidor | 40 | 192.168.40.0 | 255.255.255.0 | 192.168.40.1 |
| Impressoras de Rede | 50 | 192.168.50.0 | 255.255.255.0 | 192.168.50.1 |
| CCTV | 60 | 192.168.60.0 | 255.255.255.0 | 192.168.60.1 |
| Telefones | 70 | 192.168.70.0 | 255.255.255.0 | 192.168.70.1 |

### Esquema físico e topologia

- A rede adota uma topologia em estrela hierárquica. Cada piso tem o seu próprio bastidor de distribuição, a concentrar todas as ligações locais dos postos de trabalho, impressoras, telefones e câmaras. A interligação entre o bastidor principal (rés-do-chão) e o bastidor secundário (12.º andar) é feita por um backbone vertical em fibra ótica OM4 de alta velocidade.

| Localização | Bastidor / equipamento base | Equipamento ligado | Meio de ligação / interface |
| --- | --- | --- | --- |
| Rés-do-chão | Bastidor de chão 18U/22U (B01) | Router UDM-Pro a switch SW1 | Cabo de cobre UTP Cat6 (porta LAN 1) |
| Rés-do-chão | Bastidor de chão 18U/22U (B01) | Servidor HPE ProLiant Gen11 a switch SW1 | Cabo de cobre UTP Cat6 |
| Rés-do-chão | Bastidor de chão 18U/22U (B01) | NVR TP-Link VIGI a switch SW2 | Cabo de cobre UTP Cat6 |
| Rés-do-chão | Switch SW1 - Switch SW2 | Interligação entre switches do rés-do-chão | Cobre UTP Cat6 (EtherChannel) |
| Coluna técnica | Bastidor B01 (rés-do-chão) - Bastidor B02 (12.º andar) | Switch SW1 (rés-do-chão) a switch SW3 (12.º andar) | Cabo de fibra ótica OM4 4F (2x SFP+ LACP redundante) |
| Rés-do-chão | Switch SW1 / Switch SW2 | 16 PCs, 3 impressoras, 6 telefones, 8 câmaras | Cablagem UTP Cat6 (tomadas de parede) |
| 12.º andar | Bastidor de parede 12U (B02) | Switch SW3 a 6 PCs, 1 impressora, 1 telefone, 4 câmaras | Cablagem UTP Cat6 (tomadas de parede) |

### Etiquetagem dos pontos de rede

- Todas as extremidades dos cabos, tomadas murais RJ45 e portas nos patch panels foram identificadas e codificadas, conforme as boas práticas das normas de cablagem estruturada. Estrutura de codificação: PISO-BASTIDOR-PATCH_PANEL-PORTA para os pontos de rede e TIPO-CABO-NUMERO para o backbone.

| Zona / piso | Nomenclatura adotada | Intervalo de identificação | Descrição dos pontos |
| --- | --- | --- | --- |
| Rés-do-chão | PISO-BASTIDOR-PATCH_PANEL-PORTA | RC-B01-PP1-01 a RC-B01-PP1-17 | 17 pontos de dados (16 PCs + 1 servidor; 7 portas de reserva) |
| Rés-do-chão | PISO-BASTIDOR-PATCH_PANEL-PORTA | RC-B01-PP2-01 a RC-B01-PP2-17 | 17 pontos de voz, impressoras e câmaras (7 portas de reserva) |
| 12.º andar | PISO-BASTIDOR-PATCH_PANEL-PORTA | 12-B02-PP1-01 a 12-B02-PP1-12 | 12 pontos (6 PCs, 1 VoIP, 1 impressora, 4 câmaras; 12 portas de reserva) |
| Coluna técnica | TIPO-CABO-NUMERO | BB-FO-01 | Cabo de backbone em fibra ótica OM4 |

### Inventário de equipamentos instalados

| Categoria | Descrição do equipamento | Quantidade | Localização |
| --- | --- | --- | --- |
| Ativos de rede | Router / gateway Ubiquiti UDM-Pro | 1 | Bastidor B01 (rés-do-chão) |
| Ativos de rede | Switch Ubiquiti USW-24-POE (24 portas) | 3 | 2 no rés-do-chão (B01) / 1 no 12.º andar (B02) |
| Servidor e UPS | Servidor HPE ProLiant Gen11 (20 núcleos) | 1 | Bastidor B01 (rés-do-chão) |
| Servidor e UPS | UPS APC Smart-UPS 2200VA / Salicru 750VA | 2 | 1 no rés-do-chão (B01) / 1 no 12.º andar (B02) |
| Segurança e CCTV | Gravador NVR IP TP-Link 16 canais + disco 6 TB | 1 | Bastidor B01 (rés-do-chão) |
| Segurança e CCTV | Câmaras IP Hikvision 4 MP PoE (IPC-T240HA-LU) | 12 | 8 no rés-do-chão / 4 no 12.º andar |
| Terminais | Telefones VoIP Yealink SIP-T31P | 7 | 6 no rés-do-chão / 1 no 12.º andar |
| Terminais | Impressoras multifunções Brother MFC-L2800DW | 4 | 3 no rés-do-chão / 1 no 12.º andar |

### Prazos e garantias

- A execução do projeto rege-se por um prazo global de 15 dias úteis, contados a partir da data de adjudicação formal e da disponibilização do acesso às instalações.
- Todos os equipamentos ativos, terminais, câmaras e sistema NVR têm 3 anos de garantia direta dos respetivos fabricantes. A infraestrutura passiva de cablagem estruturada conta com 25 anos de garantia do sistema, formalizada após a entrega do relatório de certificação da rede. Os serviços de montagem e conectorização dispõem de 2 anos de garantia de instalação.
- A proposta inclui 3 meses de assistência e suporte técnico pós-venda (presencial e remoto), com um tempo de resposta de até 4 horas para incidentes críticos.

## Packet Tracer

Topologias em Cisco Packet Tracer (ficheiros `.pkt`, abrir no Packet Tracer):

- `Projeto-Post.pkt` - topologia do projeto da rede POST (VLANs, switches, router e servidor)
- `Projeto-Post-2.pkt` - segunda versão da topologia do projeto POST
- `Topologia-2.pkt` - topologia de rede local
- `Topologia-2-Copia.pkt` - cópia da topologia 2
- `Topologia-3.pkt` - topologia com router (encaminhamento inter-VLAN e NAT)
- `Modelos-Protocolo-9.pkt` - topologia para os modelos de protocolo (OSI/TCP-IP)
- `Modelos-Protocolo-10.pkt` - topologia para os modelos de protocolo (OSI/TCP-IP)

## Ficheiros

### Documentos (7 PDFs)

- `Bastidor.pdf` - bastidor 18U, servidor, switch, patch panel, UPS e cablagem
- `Bastidor-2.0.pdf` - bastidor com fibra ótica (ODF), router/firewall e ventilação
- `Rede-POST-VLANs.pdf` - projeto da rede POST: VLANs, EtherChannel, inter-VLAN, ACLs e testes
- `Configuracao-Switch-VLANs.pdf` - configuração dos switches (VLANs, trunk, portfast, port-security)
- `Configuracao-Router-NAT.pdf` - configuração do router (subinterfaces, NAT/PAT, ISP e testes)
- `Licenciamento-Windows-Server.pdf` - licenciamento Windows Server 2022 (núcleos e CALs)
- `Proposta-Infraestrutura-Dois-Escritorios.pdf` - proposta para dois escritórios (fibra, racks e CCTV)

### Imagens (6)

- `imagens/etherchannel.png` - configuração EtherChannel no switch SW2
- `imagens/acls.png` - ACLs no router R1
- `imagens/topologia-switch-vlans.png` - topologia com dois switches e quatro VLANs
- `imagens/portfast-port-security.png` - portfast e port-security no switch SW2
- `imagens/router-isp.png` - configuração do router da operadora (ISP)
- `imagens/topologia-sub-rede.png` - topologia da rede 192.168.1.0/24 com três salas

### Topologias Packet Tracer (7 ficheiros `.pkt`)

- `packet-tracer/Projeto-Post.pkt`
- `packet-tracer/Projeto-Post-2.pkt`
- `packet-tracer/Topologia-2.pkt`
- `packet-tracer/Topologia-2-Copia.pkt`
- `packet-tracer/Topologia-3.pkt`
- `packet-tracer/Modelos-Protocolo-9.pkt`
- `packet-tracer/Modelos-Protocolo-10.pkt`

## Notas

- Ambiente de laboratório e cenários fictícios. Os nomes das empresas (POST, ALGARFIVESTAR), dos departamentos e dos utilizadores são de teste.
- As imagens são capturas das topologias e das configurações em Cisco Packet Tracer e diagramas dos documentos; os PDFs ficam como prova completa.


---

## 👩‍💻 Autoria

**Alexandra Carneiro**  
*Graduanda em Engenharia de Software | Técnica Especialista em Cibersegurança & Redes*

- **GitHub:** [@alexandra-carneiro](https://github.com/alexandra-carneiro)  
- **LinkedIn:** [carneiro-alexandra](https://www.linkedin.com/in/carneiro-alexandra/)  
- **Portfólio:** [alexandracarneiro.tech](https://alexandracarneiro.tech/)
