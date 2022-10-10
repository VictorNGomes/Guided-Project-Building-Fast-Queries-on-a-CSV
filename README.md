# Guided Project: Building Fast Queries on a CSV
- Adaptado do projeto guiado do curso Algorithm Complexity da plataforma [Dataquest](https://dataquest.io)
- Usamos o dataset The Reddit Climate Change Dataset obtido por meio do [Kaggle](https://www.kaggle.com/datasets/pavellexyr/the-reddit-climate-change-dataset)
- Desenvolvido por:
  - Gabriel Lins ([GitHub](https://github.com/gabrielblins))
  - Victor Gomes ([GitHub](https://github.com/gabrielblins))


Este repósitorio contem uma resolução adaptada do *Guided Project: Building Fast Queries on a CSV* do curso *Algorithm Complexity* da plataforma Dataquest. Com o objetivo de avaliar o desempenho das funções implementadas, as quais realizam buscas de maneiras distintas. Desta forma foram realizados testes de benchmark para medir o tempo de execução das funções.

# Dataset
Para obter o dataset basta gerar um arquivo json do *Kaggle's beta API* e adicionar ao diretorio /root/.kaggle

 # Abrir CSV .
 Para abrir o arquivo CSV basta ultilizar a função *read_csv()*
 ```python
 data = read_csv('the-reddit-climate-change-dataset-comments.csv')
 ```
# Searcher
A classe Searcher implementa métodos os quais realizam as buscas.

```python
class Searcher():
    def __init__(self, csv):
        self.header = csv[0]         
        self.rows = csv[1:]
        self.id_to_row = {}
        for row in self.rows:
            self.id_to_row[row[1]] = row  

    def get_comment_from_id(self, id):   
        for row in self.rows:
            if row[self.header.index('id')] == id:
                return row
        return None

    def get_comment_from_id_fast(self,id):
        if id in self.id_to_row.keys():
            return self.id_to_row[id] 
        return None                      
      
    def get_sentiment_in_range(self, bottom ,upper):
        return [row for row in self.rows if row[-2] >= bottom and row[-2] <= upper]
  
    def twoScoreSum(self, targetSum):    
        for row1 in self.rows:                     
            for row2 in self.rows:
                if float(row1[-1]) + float(row2[-1]) == targetSum:
                    return [row1, row2]
        return -1          

    def twoScoreSum_fast(self,targetSum):
        results = {}
        for row in self.rows:
            y = targetSum - float(row[-1])
            if y in results:
                return [results[y], row]
            else:
                results[float(row[-1])] = row
        return -1
```

# Comparando Desempenho
Para comparar os tempos de execução dos métodos foi ultilizada a biblioteca [timeit](https://docs.python.org/3/library/timeit.html). 
# Resultados
[Resultados](https://colab.research.google.com/drive/1dWDPRv9bZrR1qxNy0MO47HKx7MKtGF7C#scrollTo=FkbYJ_CRrfrB)
```
============================= test session starts ==============================
platform linux -- Python 3.7.14, pytest-3.6.4, py-1.11.0, pluggy-0.7.1 -- /usr/bin/python3
cachedir: .pytest_cache
rootdir: /content, inifile:
plugins: typeguard-2.7.1
collected 8 items

test_data.py::test_get_comment_from_id PASSED                            [ 12%]
test_data.py::test_get_comment_from_id_notfound PASSED                   [ 25%]
test_data.py::test_get_comment_from_id_fast PASSED                       [ 37%]
test_data.py::test_get_comment_from_id_fast_notfound PASSED              [ 50%]
test_data.py::test_get_sentiment_in_range PASSED                         [ 62%]
test_data.py::test_twoScoreSum PASSED                                    [ 75%]
test_data.py::test_twoScoreSum_fast PASSED                               [ 87%]
test_data.py::test_twoScoreSum_fast_notfound PASSED                      [100%]

========================== 8 passed in 116.46 seconds ==========================
```
