---
name: teste-quiz
description: Executa um smoke test do quiz no navegador integrado, verificando atalhos de teclado, foco, pontuacao, progresso, feedback, temas, resultados e reinicio. Use quando o usuario pedir para testar o quiz ou validar seu funcionamento no browser.
---

# Smoke test do quiz

Teste o aplicativo real no navegador integrado. Corrija falhas confirmadas,
repita os cenarios afetados e relate apenas o que foi efetivamente verificado.
Responda no idioma do usuario.

## Preparacao

1. Leia o `index.html` para identificar perguntas, respostas corretas, seletores,
   regras de pontuacao e comportamento esperado. Nao altere o gabarito para fazer
   o teste passar.
2. Reutilize o painel do navegador informado no contexto. Se nao houver painel,
   consulte as capacidades do canvas `browser` e abra o `index.html` por uma URL
   `file://` absoluta. Este projeto nao precisa de servidor ou dependencias.
3. Consulte as capacidades antes de usar acoes desconhecidas. Use `read_page`
   para obter o estado atual e referencias antes de interagir com elementos.
4. Recarregue a pagina para comecar com um estado conhecido. Nao dependa de estado
   ou variaveis deixados por testes anteriores.

## Como interagir

- Prefira acoes estruturadas do navegador para clicar e ler a pagina.
- Use `evaluate_javascript` para assercoes de DOM, estilos computados, foco,
  eventos de teclado e percursos repetitivos quando necessario.
- Percorra a interface com cliques nos botoes e eventos. Nao chame funcoes
  internas como `selectAnswer`, `showResults` ou `renderQuestion`, nem altere
  diretamente pontuacao, indice da pergunta ou historico.
- Use resultados esperados independentes, baseados no gabarito lido do arquivo.
  Cada assercao deve falhar com uma mensagem que identifique o cenario.
- Aguarde o fim das transicoes antes de comparar cores finais. Uma leitura
  imediata pode capturar uma cor intermediaria e produzir um falso negativo.
- Para verificar animacoes, consulte seu nome durante a execucao. Respeite
  `prefers-reduced-motion`: animacoes desativadas nesse modo nao sao uma falha.
- Remova elementos auxiliares inseridos pelo teste e restaure o tema original,
  inclusive quando uma assercao falhar, usando `try/finally`.

## Cenarios obrigatorios

### Estado inicial e teclado

1. Confirme que a primeira pergunta possui quatro respostas habilitadas, zero
   pontos, progresso zero e botao de avancar desabilitado.
2. Verifique que as respostas usam botoes nativos, sem `tabindex` positivo, e
   aceitam foco.
3. Se o navegador permitir teclas nativas, exercite Tab, Shift+Tab e a ativacao
   com Enter e Espaco. Caso contrario, confira a semantica e o foco
   programaticamente, mas declare que a navegacao nativa nao foi testada.
4. Exercite os atalhos 1, 2, 3 e 4 ao longo do quiz. Confirme que escolhem a
   resposta correspondente.
5. Confirme que atalhos com Ctrl, Meta ou Alt e eventos de repeticao nao enviam
   respostas.
6. Apos responder, confirme que o foco vai para o botao de avancar. Ao avancar,
   confirme foco no titulo da nova pergunta. Nos resultados, confirme foco no
   titulo do resultado.

Eventos criados com `KeyboardEvent` sao sinteticos. Nunca os descreva como
teclas nativas ou como comprovacao de Tab, Enter ou Espaco.

### Pontuacao, progresso e feedback

1. Selecione uma resposta correta: incremente exatamente um ponto e confira
   texto, explicacao, destaque verde e animacao de acerto.
2. Confirme que todas as respostas ficam bloqueadas e o botao de avancar fica
   habilitado. Tente responder novamente por clique e atalho; a pontuacao nao
   pode mudar.
3. Avance e confirme numero da pergunta, novas respostas, feedback oculto e
   botao de avancar novamente desabilitado.
4. Selecione uma resposta incorreta: mantenha a pontuacao, confira destaque
   vermelho, animacao de tremor, explicacao e revelacao da resposta correta.
5. Confira cores de acerto e erro em ambos os temas, comparando os estilos
   computados com `--cp-success` e `--cp-danger`, sem fixar valores RGB.
6. Confira que o progresso visual e `aria-valuenow` correspondem a quantidade
   de perguntas respondidas, chegando a 100% depois da decima.
7. Confira nomes acessiveis para as respostas e uma regiao de feedback
   configurada para anuncio, como `role="status"` ou `aria-live="polite"`.
   A configuracao de ARIA nao comprova um teste com leitor de tela.

### Resultados e reinicio

1. Complete um percurso misto conhecido, por exemplo nove acertos e um erro.
   Confira pontos, percentual e dez marcadores com os estados correspondentes.
2. Confirme que a ultima pergunta oferece a acao de ver resultados.
3. Confira que o quiz fica oculto e o resultado fica visivel, com titulo,
   reacao em emoji e progresso completo.
4. Pressione atalhos de resposta no resultado; o placar deve permanecer igual.
5. Reinicie e confirme primeira pergunta, zero pontos, progresso zero,
   respostas habilitadas, feedback oculto e foco no titulo.
6. Complete tambem percursos com dez acertos e com zero acertos. Confira as
   pontuacoes, percentuais, emojis e marcadores de cada extremo.

## Falhas e encerramento

- Antes de editar, diferencie uma falha real de um problema no teste, como
  referencia obsoleta, estado residual ou transicao ainda em andamento.
- Corrija apenas falhas confirmadas e diretamente relacionadas ao comportamento
  testado, preservando acessibilidade, temas e o arquivo HTML autocontido.
- Depois de corrigir, repita o cenario afetado e os percursos que dependem dele.
- Termine com a pagina recarregada na primeira pergunta e sem residuos do teste.
- Relate de forma concisa os grupos de verificacoes aprovados, correcoes
  realizadas e limitacoes. Se algo nao foi testado ou permaneceu bloqueado,
  diga explicitamente; nao declare sucesso completo.
- Se nenhuma falha do aplicativo foi confirmada, informe que nao foi necessario
  alterar o quiz. Nao adicione dependencias, servidor ou arquivos de teste
  persistentes sem necessidade.
