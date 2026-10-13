<img src="assets/logo.svg" width="56" height="56" alt="Logo Epiciclos de Fourier" align="left">

# Epiciclos de Fourier

Desenhe um contorno e veja círculos girando refazerem o traço.

<br clear="left">

Epiciclos de Fourier é uma ferramenta em HTML estático, num arquivo só, que transforma um desenho em uma soma de círculos girando (a série de Fourier discreta do contorno). Não tem backend, build nem chave de API: tudo roda no navegador, e a imagem que você abre não sai do seu computador.

## Funcionalidades

- **Desenho à mão livre**: clique e arraste no quadro; ao soltar, o traço é fechado e a animação começa
- **Contorno a partir de imagem**: abra ou arraste um PNG, JPG ou SVG; o contorno é extraído automaticamente, com limiar ajustável e opção de inverter as cores
- **Exemplos prontos**: coração, estrela, trifólio e a letra π
- **Número de termos**: de 1 a 300 círculos, com a porcentagem da energia do contorno que eles cobrem
- **Coeficientes**: tabela com os 8 maiores coeficientes (frequência, módulo e fase)
- **Visualização**: velocidade, círculos e contorno original podem ser ligados ou desligados

## Como usar

1. Desenhe no quadro, abra uma imagem com **Abrir imagem** (ou arraste o arquivo para o quadro), ou escolha um item em **Exemplos**.
2. Se for imagem, confira a miniatura no painel **Imagem**. Se o contorno sair errado, mexa no **Limiar** ou marque **Inverter cores**.
3. Ajuste **Termos** para ver o desenho ganhar detalhe: com poucos círculos ele fica arredondado, com mais ele chega perto do original.
4. Use **Pausar** para ver a curva completa com os termos escolhidos, **Recomeçar** para reiniciar a animação e **Limpar** para apagar tudo.

## Como funciona

1. O contorno é uma lista de pontos (x, y), e cada um vira um número complexo `z = x + iy`.
2. A lista é refeita com 512 pontos à mesma distância um do outro, para que trechos desenhados devagar não pesem mais que os outros.
3. A transformada discreta de Fourier dá um coeficiente para cada frequência:

   ```
   c_n = (1/N) · Σ z_k · e^(−2πi·n·k/N)
   ```

4. Cada `c_n` é um círculo: o módulo é o raio, o argumento é o ângulo de partida e `n` é o número de voltas por ciclo (negativo gira no sentido contrário).
5. Os círculos são ordenados do maior para o menor e somados. A ponta do último desenha a curva:

   ```
   z(t) = Σ c_n · e^(2πi·n·t),  t de 0 a 1
   ```

A porcentagem de energia é a soma de `|c_n|²` dos termos usados dividida pela soma de todos.

Para imagens, o arquivo é reduzido, convertido para tons de cinza e separado em forma e fundo (o limiar inicial vem do método de Otsu). Depois a maior região conectada é escolhida e a borda dela é percorrida pixel a pixel (Moore neighbor tracing). Essa borda vira a lista de pontos do passo 1.

## Notas e limitações

- Só a borda externa é usada: um "O" vira um disco, sem o furo.
- Se a imagem tiver várias formas separadas, só a maior entra.
- A DFT é a versão direta, O(N²). Com 512 pontos isso leva poucos milissegundos, então não usei FFT.
- Nada é salvo: ao recarregar a página, o desenho se perde.
- O contador de visitas no rodapé usa o [abacus.jasoncameron.dev](https://abacus.jasoncameron.dev), um serviço público sem chave e sem garantia. Se ele não responder, aparece "n/a".

## Stack

JavaScript puro e Canvas. Sem frameworks, sem build, sem dependências.

## Licença

[MIT](LICENSE)
