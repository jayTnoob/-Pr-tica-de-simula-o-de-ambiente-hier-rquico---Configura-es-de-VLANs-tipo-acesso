# -Pr-tica-de-simula-o-de-ambiente-hier-rquico---Configura-es-de-VLANs-tipo-acesso
Essa atividade Orientada pelo professor Jhordano, teve com intuito de fazer o treinamento para fazer o "Mapeamento e Configuração das Portas de Acesso." 

# Ambiente hierárquico: VLANs de acesso (Atividade 06)

Tutorial de configuração das VLANs 10 (ALUNOS) e 20 (VISITANTES) nos switches da camada de acesso, no Cisco Packet Tracer.

- **Arquivo da simulação:** `Aula_de_switches_16_10.pkt`
- **Curso:** Redes de Computadores, IFRO
- **Disciplina:** Comutação de Redes Locais

## Sumário

1. [Visão geral da topologia](#visão-geral-da-topologia)
2. [Objetivo](#objetivo)
3. [Mapeamento das interfaces](#mapeamento-das-interfaces)
4. [Passo a passo](#passo-a-passo)
5. [Comandos usados](#comandos-usados)
6. [Verificação](#verificação)
7. [Teste de conectividade](#teste-de-conectividade)
8. [Evidências](#evidências)

## Visão geral da topologia

| Camada | Equipamentos |
|---|---|
| Borda | Roteador R-BORDA-WAN (Cisco 1841) |
| Núcleo | SW-NUCLEO-01, SW-NUCLEO-02 |
| Distribuição | SW-DIST-01, SW-DIST-02 |
| Acesso | SW-ACESSO-01 a SW-ACESSO-04 (Cisco 2960-24TT) |
| Dispositivos finais | PC1 a PC4, Laptop1 a Laptop4, Server1 |

![Topologia](diagrama.png)

## Objetivo

- Criar a VLAN 10 (ALUNOS) e a VLAN 20 (VISITANTES) em todos os switches de acesso.
- Associar as portas **ímpares** (computadores) à VLAN 10 e as portas **pares** (notebooks) à VLAN 20.

## Mapeamento das interfaces

| Switch | Hostname | Fa0/1 (ímpar, VLAN 10) | Fa0/2 (par, VLAN 20) |
|---|---|---|---|
| Switch9 | SW-ACESSO-01 | PC1 | Laptop1 |
| Switch10 | SW-ACESSO-02 | PC2 | Laptop2 |
| Switch11 | SW-ACESSO-03 | PC3 | Laptop3 |
| Switch12 | SW-ACESSO-04 | PC4 | Laptop4 |

### VLANs

| ID | Nome | Dispositivos |
|---|---|---|
| 10 | ALUNOS | PC1 a PC4 |
| 20 | VISITANTES | Laptop1 a Laptop4 |

### Endereçamento (máscara 255.255.255.0, sem gateway)

| Dispositivo | IP |
|---|---|
| PC1 a PC4 | 192.168.10.1 a 192.168.10.4 |
| Laptop1 a Laptop4 | 192.168.20.1 a 192.168.20.4 |

## Passo a passo

### Passo 1: organizar os cabos

Em cada switch de acesso, ligue o **PC na Fa0/1** e o **notebook na Fa0/2**, com cabo de cobre direto (straight-through), da `FastEthernet0` do dispositivo até a porta do switch. Deixe as demais portas sem cabo, com exceção do uplink.

### Passo 2: criar as VLANs

Acesse a CLI de cada switch de acesso (aba **CLI**) e execute:

```
enable
configure terminal
vlan 10
 name ALUNOS
 exit
vlan 20
 name VISITANTES
 exit
end
```

### Passo 3: associar as portas às VLANs

Execute nos **quatro** switches de acesso (SW-ACESSO-01 a SW-ACESSO-04):

```
configure terminal
interface fastEthernet 0/1
 switchport mode access
 switchport access vlan 10
 exit
interface fastEthernet 0/2
 switchport mode access
 switchport access vlan 20
 exit
end
```

### Passo 4: salvar a configuração

```
copy running-config startup-config
```

### Passo 5: configurar o IP dos dispositivos

Em cada PC e notebook, abra **Desktop > IP Configuration**, escolha **Static** e preencha o IP e a máscara conforme a tabela de endereçamento. O gateway fica em branco.

## Comandos usados

| Comando | Função |
|---|---|
| `vlan <id>` | Cria a VLAN com o ID informado |
| `name <nome>` | Define o nome da VLAN |
| `interface fastEthernet 0/<n>` | Entra na configuração da porta |
| `switchport mode access` | Define a porta como porta de acesso |
| `switchport access vlan <id>` | Associa a porta à VLAN |
| `show vlan brief` | Lista as VLANs e as portas associadas |
| `show interfaces status` | Mostra estado, VLAN e modo de cada porta |

## Verificação

Em cada switch de acesso:

```
show vlan brief
show interfaces status
```

Resultado esperado:

| Item | Esperado |
|---|---|
| VLAN 10 (ALUNOS) | `active`, com a porta `Fa0/1` |
| VLAN 20 (VISITANTES) | `active`, com a porta `Fa0/2` |
| Fa0/1 e Fa0/2 em `show interfaces status` | `connected`, VLAN 10 e VLAN 20 respectivamente |

## Teste de conectividade

O ping só funciona entre dispositivos da **mesma VLAN**. Para os testes de ponta a ponta entre ramos diferentes, os trunks entre os switches precisam estar configurados.

Use **Desktop > Command Prompt** em cada dispositivo.

| Teste | Origem | Comando | Esperado |
|---|---|---|---|
| Mesma VLAN 10 | PC1 | `ping 192.168.10.4` | Responde |
| Mesma VLAN 20 | Laptop1 | `ping 192.168.20.4` | Responde |
| VLANs diferentes | PC1 | `ping 192.168.20.1` | Falha (`Request timed out`) |

O primeiro ping pode dar timeout por causa do ARP; repita uma vez antes de concluir.

## Evidências

- [ ] `show vlan brief` dos quatro switches de acesso
- [ ] `show interfaces status` dos quatro switches de acesso
- [ ] `ipconfig` de PC1, PC4, Laptop1 e Laptop4
- [ ] Ping entre dispositivos da mesma VLAN (com a barra de título do dispositivo visível)
- [ ] Ping entre VLANs diferentes falhando
- [ ] Topologia final, com os cabos verdes

## Autor

João Arthur Amorim Maciel
