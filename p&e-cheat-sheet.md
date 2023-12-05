# Cheat Sheet

---

## Calcular medidas de dispersão e variabilidade e gerar um histograma

```r
# Outra opção: as.numeric(names(subset(table(notas), table(notas) == max(table(notas)))))
get_mode = function(df) {
  freq = table(df)
  result = as.numeric(names(freq[freq == max(freq)]))
  return (result)
}

# Ler e separar os dados
setwd("/home/arthurpmrs/ufal/cc/materias/periodo_3/estatistica/exercicios")
data = read.csv("ENEM22.csv", sep=";", header=TRUE, dec=".")
notas = data$NU_NOTA_ENEM


# medidas descritivas (ou tendência central)
descritivas = data.frame(
  Propriedades = c("Max", "Min", "Média", "Mediana", "Q1", "Q3", "Moda"),
  Valores = round(c(max(notas),
                    min(notas),
                    mean(notas),
                    median(notas),
                    quantile(notas, 0.25),
                    quantile(notas, 0.75),
                    get_mode(notas)),
                  2)
)
print(descritivas)

# medidas de dispersão (ou variabilidade)
dispersao = data.frame(
  Propriedades = c("Amplitude", "Variância", "Desvio Padrão", "CV"),
  Valores = round(c(max(notas) - min(notas),
                    var(notas),
                    sd(notas),
                    sd(notas)/mean(notas) * 100),
                  2)
)
print(dispersao)

# Histograma
h = hist(notas, breaks=c(300, 400, 500, 600, 700, 800), labels=TRUE,
         col="aliceblue", main="Notas Enem 2022 - Alagoas",
         xlim=c(300, 800), ylim=c(0, 600))
grid(col = "gray", lty = "dotted")

# Result
  Propriedades Valores
1          Max  768.38
2          Min  372.24
3        Média  516.85
4      Mediana  506.99
5           Q1  458.32
6           Q3  570.76
7         Moda  511.72

   Propriedades Valores
1     Amplitude  396.14
2     Variância 5419.66
3 Desvio Padrão   73.62
4            CV   14.24
```

[Gráficos](attachment/cheat-sheet/20231205190431.png)

## Gerar um boxplot múltiplo

```R
# Ler e separar os dados
setwd("/home/arthurpmrs/ufal/cc/materias/periodo_3/estatistica/exercicios")
data = read.csv("ENEM22.csv", sep=";", header=TRUE, dec=".")

notas = cbind(humanas=data$NU_NOTA_CH,
              natureza=data$NU_NOTA_CN,
              linguagens=data$NU_NOTA_LC,
              matematica=data$NU_NOTA_MT,
              redacao=data$NU_NOTA_REDACAO,
              media=data$NU_NOTA_ENEM)

bp = boxplot(notas, col=c("azure", "cyan3", "aliceblue", "darkorchid",
                          "bisque", "cornflowerblue"), ylim=c(200, 1000))
```

[Gráficos](attachment/cheat-sheet/20231204081853.png)

## Gerar uma tabela de frequência completa com classes equidistantes

```R
get_freq_table = function(df) {
  # Sturges
  n_classes = round(1 + 3.322 * log10(length(df)), digits=0)

  amp = max(df) - min(df)
  amp_classes = round(amp / n_classes, digits=0)

  classes = seq(floor(min(df)), ceiling(max(df)), amp_classes)

  if (max(df) > max(classes)) {
    classes = c(classes, max(classes) + amp_classes)
  }

  classes_label = c()
  for (i in 1:(length(classes) - 1)) {
    classes_label = c(classes_label, paste0(classes[i], " |- ", classes[i + 1]))
  }

  freq = table(cut(df, breaks=classes, labels=classes_label, right=FALSE))
  freq_ac = cumsum(freq)
  freq_rel = prop.table(freq)
  freq_rel_rounded = round(freq_rel, digits=2)
  freq_ac_rel = prop.table(freq_ac)
  freq_ac_rel_rounded = round(freq_ac_rel, digits=2)

  density = round(freq_rel / amp_classes, digits=4)

  freq_table = cbind(f=freq, fac=freq_ac, fr=freq_rel_rounded,
                     frac=freq_ac_rel_rounded,
                     A=amp_classes, densidade=density)

  return(list(ft=as.data.frame(freq_table), classes=classes))
}

# Ler e separar os dados
setwd("/home/arthurpmrs/ufal/cc/materias/periodo_3/estatistica/exercicios")
data = read.csv("ENEM22.csv", sep=";", header=TRUE, dec=".")

notas = data$NU_NOTA_ENEM

result = get_freq_table(notas)
print(result$ft)

h = hist(notas, breaks=result$classes, col="aliceblue", axes=FALSE,
         labels=TRUE, ylim=c(0, 300))
axis(side = 1, at = result$classes)
axis(side = 2)

# Results
             f  fac   fr frac  A densidade
372 |- 408  45   45 0.04 0.00 36    0.0011
408 |- 444 148  193 0.13 0.02 36    0.0037
444 |- 480 231  424 0.21 0.04 36    0.0057
480 |- 516 184  608 0.16 0.06 36    0.0045
516 |- 552 158  766 0.14 0.08 36    0.0039
552 |- 588 153  919 0.14 0.10 36    0.0038
588 |- 624 111 1030 0.10 0.11 36    0.0027
624 |- 660  54 1084 0.05 0.11 36    0.0013
660 |- 696  29 1113 0.03 0.12 36    0.0007
696 |- 732  10 1123 0.01 0.12 36    0.0002
732 |- 768   2 1125 0.00 0.12 36    0.0000
768 |- 804   1 1126 0.00 0.12 36    0.0000
```

[Gráficos](attachment/cheat-sheet/20231204095359.png)

## Gráfico de barras com variáveis quantitativas e qualitativas

> [!NOTE]
> É necessário separar a variável quantitativa em classes para facilitar a visualização.

```r
# Ler e separar os dados
setwd("/home/arthurpmrs/ufal/cc/materias/periodo_3/estatistica/exercicios")
data = read.csv("ENEM22.csv", sep=";", header=TRUE, dec=".")

# Tratamento dos dados
data$INTERNET[data$INTERNET == "N\xe3o"] = "Nao"

# Separação dos dados
internet = data$INTERNET
nota = data$NU_NOTA_ENEM
nota_cut = cut(nota, breaks=quantile(nota), right=FALSE)

# Gerar matrix
matrix = table(internet, nota_cut)
matrix_rel = round(addmargins(prop.table(matrix)), 2)
print(matrix_rel)

# Histograma
h = barplot(matrix, col=c("aliceblue", "aquamarine2"), beside=TRUE, legend=TRUE,
            ylim=c(0, 300), main="Notas ENEM vs Acesso a Internet")
grid(col = "gray", lty = "dotted")

# Result
        nota_cut
internet [372,458) [458,507) [507,571) [571,768)  Sum
     Nao      0.10      0.08      0.07      0.04 0.29
     Sim      0.15      0.17      0.18      0.21 0.71
     Sum      0.25      0.25      0.25      0.25 1.00
```

[Gráficos](attachment/cheat-sheet/20231204102316.png)

## Matriz de Correlação, Gráfico de correlação e Gráfico de dispersão

```r
# Ler e separar os dados
setwd("/home/arthurpmrs/ufal/cc/materias/periodo_3/estatistica/exercicios")
data = read.csv("ENEM22.csv", sep=";", header=TRUE, dec=".")

notas = cbind(humanas=data$NU_NOTA_CH,
              natureza=data$NU_NOTA_CN,
              linguagens=data$NU_NOTA_LC,
              matematica=data$NU_NOTA_MT,
              redacao=data$NU_NOTA_REDACAO,
              media=data$NU_NOTA_ENEM)

matrix_cor = signif(cor(notas), 4)
print(matrix_cor)

library(corrplot)
corrplot(matrix_cor)

R = cor(data$NU_NOTA_REDACAO, data$NU_NOTA_ENEM)
plot(data$NU_NOTA_REDACAO, data$NU_NOTA_ENEM,
     main=paste("Nota Redação vs Média ENEM R=", as.character(round(R, 4))),
     col="cyan3", lwd=2, pch=21, xlab="nota redacao", ylab="media enem")

abline(lsfit(data$NU_NOTA_REDACAO, data$NU_NOTA_ENEM), col="darkorchid")

R_2 = cor(data$NU_NOTA_CH, data$NU_NOTA_LC)
plot(data$NU_NOTA_REDACAO, data$NU_NOTA_ENEM,
     main=paste("Humanas vs Linguagens ENEM R=", as.character(round(R_2, 4))),
     col="cyan3", lwd=2, pch=21, xlab="nota redacao", ylab="nota linguagens")

abline(lsfit(data$NU_NOTA_REDACAO, data$NU_NOTA_ENEM), col="darkorchid")


# Result
           humanas natureza linguagens matematica redacao  media
humanas     1.0000   0.6111     0.7093     0.5713  0.5472 0.8127
natureza    0.6111   1.0000     0.6208     0.6034  0.4965 0.7808
linguagens  0.7093   0.6208     1.0000     0.5465  0.5406 0.7954
matematica  0.5713   0.6034     0.5465     1.0000  0.5131 0.7993
redacao     0.5472   0.4965     0.5406     0.5131  1.0000 0.8375
media       0.8127   0.7808     0.7954     0.7993  0.8375 1.0000
```

[Gráficos](attachment/cheat-sheet/20231205191231.png)

[Gráficos](attachment/cheat-sheet/20231205191250.png)

[Gráficos](attachment/cheat-sheet/20231205191342.png)
