# Roadmap de Experimentos Multianuais

## Objetivo

Treinar e avaliar o nowcasting com anos inteiros, separados temporalmente,
para ampliar a diversidade de eventos de chuva e evitar vazamento entre
treino, validação e teste.

## Estado Atual

- [x] Integração de targets AlertaRio em memmap.
- [x] Dataset 2024 reduzido para `128 x 128`.
- [x] Smoke test em GPU com entrada `[batch, 3, 5, 128, 128]` e saída
  `[batch, 1, 5, 128, 128]`.
- [x] Losses mascaradas e ponderadas, sampler balanceado e métricas por faixa
  implementados no pacote do projeto.
- [x] Runner independente do projeto criado em
  `scripts/train_nowcasting.py`.
- [ ] Dataset `128 x 128` AlertaRio disponível e validado para 2012-2024.
- [ ] Primeiro experimento com split temporal multianual.

## Protocolo Inicial

| Split | Anos | Uso |
|---|---|---|
| Treino | 2012-2021 | Otimização dos parâmetros |
| Validação | 2022 | Early stopping e escolha de hiperparâmetros |
| Teste | 2023-2024 | Avaliação final |

Os anos devem ser revisados após uma auditoria de cobertura de radar e de
estações. O runner rejeita anos repetidos entre splits.

## Etapas

### Dataset

- [ ] Conferir `radar_frames.dat`, `radar_timestamps.npy`, `metadata.json`,
  `Y_alertario.dat`, `M_alertario.dat` e seus metadados em cada ano.
- [ ] Gerar targets AlertaRio ausentes.
- [ ] Executar o downsample para 2012-2023 sem sobrescrever 2024.
- [ ] Registrar contagem de observações válidas e distribuição por faixa.

### Código

- [x] Remover o split interno 60/20/20 do caminho novo de treinamento.
- [x] Implementar `--train-years`, `--val-years` e `--test-years`.
- [x] Restringir o sampler balanceado ao treino.
- [x] Registrar configuração, commit do core, checkpoints, histórico e métricas.
- [ ] Executar os testes de integração no ambiente `ailab` da Skat.

### Matriz Experimental

| ID | Loss | Sampler | Repetições iniciais |
|---|---|---|---:|
| M1 | `masked-mae` | não | 1 |
| M2 | `masked-huber` | não | 1 |
| M3 | `weighted-huber` | não | 1 |
| M4 | `weighted-huber` | sim | 1 |

As configurações finalistas devem ser repetidas com 2 ou 3 seeds. Métricas
globais devem ser complementadas por resultados por horizonte e intensidade.

## Registro

| Data | Item | Estado | Evidência |
|---|---|---|---|
| 2026-09-10 | Dataset AlertaRio 2024 em `128 x 128` | Concluído | 25.505 frames e 3,2 GB. |
| 2026-09-10 | Smoke test 2024 em GPU | Concluído | Treinamento, validação e teste concluídos. |
| 2026-09-10 | `weighted-huber` + sampler em 2024 | Concluído | Duas repetições exploratórias. |
| 2026-09-10 | Migração da lógica de domínio | Em andamento | Código movido para `src/nowcasting`. |
