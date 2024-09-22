import numpy as np
import random

# Função objetivo
def f(x):
    return x**3 - 6*x + 14

# Converter valor de x para binário
def to_binary(x, bits):
    return format(int((x + 10) * (2**bits / 20)), f'0{bits}b')

# Converter binário de volta para valor de x
def from_binary(binary):
    decimal = int(binary, 2)
    return decimal * (20 / (2**len(binary))) - 10

# Criar população inicial
def inicializar_populacao(tamanho_populacao, bits):
    return [to_binary(random.uniform(-10, 10), bits) for _ in range(tamanho_populacao)]

# Seleção por torneio
def selecao_torneio(populacao, n_torneios=2):
    vencedores = []
    for _ in range(len(populacao)):
        competidores = random.sample(populacao, n_torneios)
        vencedores.append(min(competidores, key=lambda ind: f(from_binary(ind))))
    return vencedores

# Crossover de um ponto
def crossover(pai1, pai2):
    ponto = random.randint(1, len(pai1) - 1)
    return pai1[:ponto] + pai2[ponto:]

# Mutação
def mutar(individuo, taxa_mutacao):
    return ''.join(
        bit if random.random() > taxa_mutacao else str(1 - int(bit))
        for bit in individuo
    )

# Algoritmo Genético
def algoritmo_genetico(tamanho_populacao=10, taxa_mutacao=0.01, geracoes=100, bits=10,
                       elitismo=False, percentual_elitismo=0.1):
    
    populacao = inicializar_populacao(tamanho_populacao, bits)
    
    for geracao in range(geracoes):
        populacao = sorted(populacao, key=lambda ind: f(from_binary(ind)))
        
        if elitismo:
            n_elitistas = int(percentual_elitismo * tamanho_populacao)
            nova_populacao = populacao[:n_elitistas]
        else:
            nova_populacao = []

        while len(nova_populacao) < tamanho_populacao:
            pai1, pai2 = random.choices(populacao[:len(populacao)//2], k=2)  # Seleção
            filho = crossover(pai1, pai2)
            filho = mutar(filho, taxa_mutacao)
            nova_populacao.append(filho)

        populacao = nova_populacao

    melhor_individuo = min(populacao, key=lambda ind: f(from_binary(ind)))
    melhor_valor = from_binary(melhor_individuo)
    return melhor_valor, f(melhor_valor)

# Exemplo de uso
melhor_x, valor_minimo = algoritmo_genetico()
print(f'Melhor x: {melhor_x}, f(x): {valor_minimo}')
