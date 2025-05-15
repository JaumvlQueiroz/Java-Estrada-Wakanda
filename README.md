# Java-Estrada-Wakanda
Este código foi inicialmente feio em C para um trabalho de faculdade, mas quis transicionar de C, para Java.
// Cidade.java - Classe para informações da cidade
public class Cidade {
    private String nome;
    private int posicao;
    echo "# Java-Estrada-Wakanda" >> README.md
    public Cidade(String nome, int posicao) {
        this.nome = nome;
        this.posicao = posicao;
    }

    public String getNome() {
        return nome;
    }

    public int getPosicao() {
        return posicao;
    }

    @Override
    public String toString() {
        return nome + " (posição: " + posicao + ")";
    }
}

// Estrada.java - Classe que representa a estrada com cidades
public class Estrada {
    private int n; // Número de cidades
    private int t; // Comprimento da estrada
    private Cidade[] cidades; // Vetor de cidades

    public Estrada(int t, int n, Cidade[] cidades) {
        this.n = n;
        this.t = t;
        this.cidades = cidades;
    }

    public int getN() {
        return n;
    }

    public int getT() {
        return t;
    }

    public Cidade[] getCidades() {
        return cidades;
    }
}

// EstradaDeWakanda.java - Classe principal que implementa a funcionalidade requerida
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.util.Arrays;

public class EstradaDeWakanda {
    
    /**
     * Inicializa a estrada a partir de um arquivo
     * @param nomeArquivo Nome do arquivo para leitura
     * @return Objeto Estrada com os dados da estrada ou null se houver erro
     */
    public static Estrada getEstrada(String nomeArquivo) {
        try (BufferedReader leitor = new BufferedReader(new FileReader(nomeArquivo))) {
            // Lê o comprimento da estrada
            int t = Integer.parseInt(leitor.readLine().trim());
            
            // Lê o número de cidades
            int n = Integer.parseInt(leitor.readLine().trim());
            
            // Valida as restrições
            if (t < 3 || t > Math.pow(10, 6) || n < 2 || n > Math.pow(10, 4)) {
                return null;
            }
            
            Cidade[] cidades = new Cidade[n];
            
            // Lê as informações das cidades
            for (int i = 0; i < n; i++) {
                String[] infoCidade = leitor.readLine().trim().split(" ", 2);
                int posicao = Integer.parseInt(infoCidade[0]);
                String nome = infoCidade[1];
                
                // Valida posição da cidade
                if (posicao <= 0 || posicao >= t) {
                    return null;
                }
                
                // Verifica se há posições duplicadas
                for (int j = 0; j < i; j++) {
                    if (cidades[j].getPosicao() == posicao) {
                        return null;
                    }
                }
                
                cidades[i] = new Cidade(nome, posicao);
            }
            
            // Ordena as cidades por posição para facilitar cálculos posteriores
            Arrays.sort(cidades, (c1, c2) -> Integer.compare(c1.getPosicao(), c2.getPosicao()));
            
            return new Estrada(t, n, cidades);
            
        } catch (IOException | NumberFormatException e) {
            return null;
        }
    }
    
    /**
     * Calcula a menor vizinhança entre todas as cidades
     * @param nomeArquivo Nome do arquivo com os dados da estrada
     * @return O valor da menor vizinhança ou -1 se houver erro
     */
    public static double calcularMenorVizinhanca(String nomeArquivo) {
        Estrada estrada = getEstrada(nomeArquivo);
        if (estrada == null) {
            return -1;
        }
        
        return calcularMenorVizinhanca(estrada);
    }
    
    /**
     * Método auxiliar para calcular a menor vizinhança
     * @param estrada Objeto estrada com os dados necessários
     * @return O valor da menor vizinhança
     */
    private static double calcularMenorVizinhanca(Estrada estrada) {
        Cidade[] cidades = estrada.getCidades();
        int n = estrada.getN();
        int t = estrada.getT();
        
        double menorVizinhanca = Double.MAX_VALUE;
        
        // Calcula a vizinhança para cada cidade
        for (int i = 0; i < n; i++) {
            double vizinhanca = calcularVizinhanca(i, cidades, n, t);
            if (vizinhanca < menorVizinhanca) {
                menorVizinhanca = vizinhanca;
            }
        }
        
        return menorVizinhanca;
    }
    
    /**
     * Calcula a vizinhança de uma cidade específica
     * @param indice Índice da cidade no vetor
     * @param cidades Vetor de cidades
     * @param n Número de cidades
     * @param t Comprimento da estrada
     * @return O valor da vizinhança da cidade
     */
    private static double calcularVizinhanca(int indice, Cidade[] cidades, int n, int t) {
        Cidade cidade = cidades[indice];
        int posicao = cidade.getPosicao();
        double inicioVizinhanca, fimVizinhanca;
        
        // Calcula o início da vizinhança
        if (indice == 0) {
            // Primeira cidade - início da estrada até o ponto médio com a próxima cidade
            inicioVizinhanca = 0;
        } else {
            // Ponto médio com a cidade anterior
            inicioVizinhanca = (posicao + cidades[indice - 1].getPosicao()) / 2.0;
        }
        
        // Calcula o fim da vizinhança
        if (indice == n - 1) {
            // Última cidade - ponto médio com a cidade anterior até o fim da estrada
            fimVizinhanca = t;
        } else {
            // Ponto médio com a próxima cidade
            fimVizinhanca = (posicao + cidades[indice + 1].getPosicao()) / 2.0;
        }
        
        return fimVizinhanca - inicioVizinhanca;
    }
    
    /**
     * Retorna o nome da cidade com a menor vizinhança
     * @param nomeArquivo Nome do arquivo com os dados da estrada
     * @return O nome da cidade com menor vizinhança ou null se houver erro
     */
    public static String cidadeMenorVizinhanca(String nomeArquivo) {
        Estrada estrada = getEstrada(nomeArquivo);
        if (estrada == null) {
            return null;
        }
        
        Cidade[] cidades = estrada.getCidades();
        int n = estrada.getN();
        int t = estrada.getT();
        
        double menorVizinhanca = Double.MAX_VALUE;
        String nomeCidadeMenorVizinhanca = null;
        
        // Encontra a cidade com a menor vizinhança
        for (int i = 0; i < n; i++) {
            double vizinhanca = calcularVizinhanca(i, cidades, n, t);
            if (vizinhanca < menorVizinhanca) {
                menorVizinhanca = vizinhanca;
                nomeCidadeMenorVizinhanca = cidades[i].getNome();
            }
        }
        
        return nomeCidadeMenorVizinhanca;
    }
    
    /**
     * Método principal para teste
     */
    public static void main(String[] args) {
        String nomeArquivo = "Teste01.txt";
        
        Estrada estrada = getEstrada(nomeArquivo);
        if (estrada != null) {
            System.out.println("Estrada lida com sucesso:");
            System.out.println("Comprimento da estrada: " + estrada.getT());
            System.out.println("Número de cidades: " + estrada.getN());
            System.out.println("Cidades:");
            
            for (Cidade cidade : estrada.getCidades()) {
                System.out.println("- " + cidade);
            }
            
            System.out.println("\nMenor vizinhança: " + calcularMenorVizinhanca(nomeArquivo));
            System.out.println("Cidade com menor vizinhança: " + cidadeMenorVizinhanca(nomeArquivo));
        } else {
            System.out.println("Erro ao ler o arquivo ou dados inválidos.");
        }
    }
}
