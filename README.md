# TrabalhoFIA
 #Zonas:
#| Zona       | Tipo    | País     | Prioridade | Acesso                    | Clima (normal) |
#| ---------- | ------- | -------- | ---------- | ------------------------- | -------------- |
#| Lisboa     | Urbana  | Portugal | Alta       | Autocarro, comboio, metro | Variável       |
#| Porto      | Urbana  | Portugal | Alta       | Autocarro, comboio        | Variável       |
#| Sevilha    | Urbana  | Espanha  | Alta       | Autocarro, comboio        | Quente         |
#| Madrid     | Urbana  | Espanha  | Alta       | Autocarro, comboio, avião | Variável       |
#| Rabat      | Urbana  | Marrocos | Alta       | Autocarro, avião          | Calor intenso  |
#| Casablanca | Urbana  | Marrocos | Alta       | Autocarro, comboio, avião | Quente seco    |
#| Douro      | Rural   | Portugal | Média      | Autocarro                 | Nevoeiro       |
#| Atlas      | Isolada | Marrocos | Alta       | Avião                     | Montanhas      |
#| Alentejo   | Rural   | Portugal | Baixa      | Autocarro                 | Calor seco     |

 #Veículos:
#Autocarros (10): 50 passageiros, 600 km de autonomia, 80 km/h
#Comboios (5): 200 passageiros, elétrico, 120 km/h
#Metro (apenas Lisboa): 300 passageiros, elétrico, 40 km/h
#Aviões (3): 150 passageiros, 1500 km de autonomia, 600 km/h


arestas = {
    "Lisboa": [
        ("Porto", 313),
        ("Douro", 125),
        ("Alentejo", 170),
        ("Madrid", 640)
    ],
    "Porto": [("Lisboa", 313), ("Douro", 100)],
    "Madrid": [("Lisboa", 640), ("Sevilha", 530)],
    "Sevilha": [("Madrid", 530), ("Rabat", 700)],
    "Rabat": [("Sevilha", 700), ("Casablanca", 95), ("Atlas", 300)],
}


# Representa uma zona no mapa
class Zona:
    def __init__(self, nome, tipo, prioridade, acessos, clima):
        self.nome = nome
        self.tipo = tipo
        self.prioridade = prioridade
        self.acessos = acessos  # Tipos de transporte permitidos
        self.clima = clima

# Representa uma ligação entre zonas
class Ligacao:
    def __init__(self, destino, distancia_km):
        self.destino = destino
        self.distancia = distancia_km

# Representa um veículo
class Veiculo:
    def __init__(self, tipo, capacidade, autonomia_km, velocidade_kmh):
        self.tipo = tipo
        self.capacidade = capacidade
        self.autonomia = autonomia_km
        self.velocidade = velocidade_kmh


zonas = {
    "Lisboa": Zona("Lisboa", "Urbana", "Alta", ["autocarro", "comboio", "metro"], "Variável"),
    "Porto": Zona("Porto", "Urbana", "Alta", ["autocarro", "comboio"], "Variável"),
    "Sevilha": Zona("Sevilha", "Urbana", "Alta", ["autocarro", "comboio"], "Quente"),
    "Madrid": Zona("Madrid", "Urbana", "Alta", ["autocarro", "comboio", "avião"], "Variável"),
    "Rabat": Zona("Rabat", "Urbana", "Alta", ["autocarro", "avião"], "Calor intenso"),
    "Casablanca": Zona("Casablanca", "Urbana", "Alta", ["autocarro", "comboio", "avião"], "Quente seco"),
    "Douro": Zona("Douro", "Rural", "Média", ["autocarro"], "Nevoeiro"),
    "Atlas": Zona("Atlas", "Isolada", "Alta", ["avião"], "Montanhas"),
    "Alentejo": Zona("Alentejo", "Rural", "Baixa", ["autocarro"], "Calor seco")
}

grafo = {
    "Lisboa": [Ligacao("Porto", 313), Ligacao("Douro", 125), Ligacao("Alentejo", 170), Ligacao("Madrid", 640)],
    "Porto": [Ligacao("Lisboa", 313), Ligacao("Douro", 100)],
    "Douro": [Ligacao("Lisboa", 125), Ligacao("Porto", 100)],
    "Alentejo": [Ligacao("Lisboa", 170)],
    "Madrid": [Ligacao("Lisboa", 640), Ligacao("Sevilha", 530)],
    "Sevilha": [Ligacao("Madrid", 530), Ligacao("Rabat", 700)],
    "Rabat": [Ligacao("Sevilha", 700), Ligacao("Casablanca", 95), Ligacao("Atlas", 300)],
    "Casablanca": [Ligacao("Rabat", 95)],
    "Atlas": [Ligacao("Rabat", 300)]
}


veiculos = [
    Veiculo("autocarro", 50, 600, 80),
    Veiculo("comboio", 200, float("inf"), 120),
    Veiculo("metro", 300, float("inf"), 40),
    Veiculo("avião", 150, 1500, 600)
]

import heapq

def ucs(grafo, inicio, objetivo):
    # Fila de prioridade: (custo acumulado, zona atual, caminho até agora)
    fronteira = [(0, inicio, [inicio])]
    visitados = set()

    while fronteira:
        custo_atual, zona_atual, caminho = heapq.heappop(fronteira)

        if zona_atual == objetivo:
            return caminho, custo_atual

        if zona_atual in visitados:
            continue
        visitados.add(zona_atual)

        for ligacao in grafo.get(zona_atual, []):
            if ligacao.destino not in visitados:
                novo_custo = custo_atual + ligacao.distancia
                novo_caminho = caminho + [ligacao.destino]
                heapq.heappush(fronteira, (novo_custo, ligacao.destino, novo_caminho))

    return None, float('inf')  # se não houver caminho
