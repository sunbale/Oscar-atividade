# Oscar-atividade
atividade do mongodb, perguntas e respostas estão Aqui
obs: no meu arquivo o nome está apenas oscar, por isso os comando ao invés de oscar_indicados está como oscar.

Nível 1: Primeiros Passos
Conhecendo a Base de Dados

# 1.1 Quantos registros existem na coleção de indicados ao Oscar?

R: 10889 registros

db.oscar.countDocuments();

# 1.2 Quais são as diferentes categorias de premiação que existem no banco de dados? Liste todas as categorias únicas.

R: 115 registros

db.oscar.distinct("categoria");
#1.3 Qual foi o primeiro ano de cerimônia do Oscar registrado na base?

R: 1500

db.oscar.find().skip(15).limit(1).sort({ano_cerimonia: -1})
# 1.4 Qual foi o último ano de cerimônia registrado na base?
R: 1928
db.oscar.find().skip(15).limit(1).sort({ano_cerimonia: 1})

# 1.5 Quantas cerimônias do Oscar estão registradas no total?
R: 97
db.oscar.distinct("ano_cerimonia").length

# 1.6 Atualize os registros da coleção com os dados do Oscar 2025 e 2026 (pesquise os indicados e adicione-os).
(Indicados ao oscar adicionados ao documento)
R: 2025: 120 indicados; 2026: 125 indicados

db.oscar.countDocuments({ ano_cerimonia: 2025 })   // esperado: 120
db.oscar.countDocuments({ ano_cerimonia: 2026 })   // esperado: 125

## Nível 2: Explorando Categorias
#2.1 Quantas indicações existem para cada categoria? Agrupe por categoria e ordene da mais frequente para a menos frequente.
R:   directing: 479; film editing: 460; ACTOR IN A SUPPORTING ROLE: 450; ACTRESS IN A SUPPORTING ROLE: 450; best picture: 390; DOCUMENTARY (Short Subject): 378; CINEMATOGRAPHY: 348; DOCUMENTARY (Feature): 345; ART DIRECTION: 307; COSTUME DESIGN: 305; MUSIC (Original Score): 280; SOUND: 255; ACTOR IN A LEADING ROLE: 250; ACTRESS IN A LEADING ROLE: 250; MUSIC (Original Song): 245; SHORT FILM (Live Action):236; ACTRESS:236; ACTOR: 232; SHORT FILM (Animated):225.

db.oscar.aggregate([
  { $group: { _id: "$categoria", total: { $sum: 1 } } },
  { $sort: { total: -1 } }
])

# 2.2 Qual categoria teve mais indicações ao longo da história do Oscar?
R: DIRECTING: 479
db.oscar.aggregate([
  { $group: { _id: "$categoria", total: { $sum: 1 } } },
  { $sort: { total: -1 } },
  { $limit: 1 }
])
# 2.3 Qual categoria teve menos indicações ao longo da história?
R: SHORT FILM (Animated):225
db.oscar.aggregate([
  { $group: { _id: "$categoria", total: { $sum: 1 } } },
  { $sort: { total: 1 } },
  { $limit: 1 }
])

# 2.4 A partir de que ano a categoria "ACTRESS" deixou de existir? (Dica: procure a última cerimônia com essa categoria)
R: 1976
db.oscar.find({ categoria: "ACTRESS" }).sort({ ano_cerimonia: -1 }).limit(1)

# 2.5 Quais categorias existiam na primeira cerimônia (1928) e não existem mais hoje?
R:  'ACTOR',
  'ACTRESS',
  'ART DIRECTION',
  'DIRECTING (Comedy Picture)',
  'DIRECTING (Dramatic Picture)',
  'ENGINEERING EFFECTS',
  'OUTSTANDING PICTURE',
  'SPECIAL AWARD',
  'UNIQUE AND ARTISTIC PICTURE',
  'WRITING (Adaptation)',
  'WRITING (Original Story)',
  'WRITING (Title Writing)'

const antigas = db.oscar.distinct("categoria", { ano_cerimonia: 1928 });
const atuais = db.oscar.distinct("categoria", { ano_cerimonia: 2026 });
antigas.filter(c => !atuais.includes(c))
  
# 2.6 Liste todas as categorias que contêm a palavra "DIRECTING" no nome.
R:  'DIRECTING',
  'DIRECTING (Comedy Picture)',
  'DIRECTING (Dramatic Picture)'

  db.oscar.distinct("categoria", { categoria: /DIRECTING/ })

  Nível 3: Atores e Atrizes Famosos
Natalie Portman

# 3.1 Quantas vezes Natalie Portman foi indicada ao Oscar?
R: 3
db.oscar.countDocuments({ nome_do_indicado: "Natalie Portman" })

# 3.2 Quantos Oscars Natalie Portman ganhou?
R: 1
db.oscar.countDocuments({ nome_do_indicado: "Natalie Portman", vencedor: 1 })

# 3.3 Em quais anos e por quais filmes Natalie Portman foi indicada?
R: ano_cerimonia: 2005,
  nome_do_filme: 'Closer'
   ano_cerimonia: 2011,
  nome_do_filme: 'Black Swan'
  ano_cerimonia: 2017,
  nome_do_filme: 'Jackie'

  db.oscar.find(
  { nome_do_indicado: "Natalie Portman" },
  { ano_cerimonia: 1, nome_do_filme: 1, _id: 0 }
).sort({ ano_cerimonia: 1 })


# 3.4 Liste todas as indicações de Natalie Portman mostrando: ano, categoria, filme e se venceu.
R: ano_cerimonia: 2005,
  categoria: 'ACTRESS IN A SUPPORTING ROLE',
  nome_do_filme: 'Closer',
  vencedor: 0
    ano_cerimonia: 2011,
  categoria: 'ACTRESS IN A LEADING ROLE',
  nome_do_filme: 'Black Swan',
  vencedor: 1
    ano_cerimonia: 2017,
  categoria: 'ACTRESS IN A LEADING ROLE',
  nome_do_filme: 'Jackie',
  vencedor: 0

  db.oscar.find(
  { nome_do_indicado: "Natalie Portman" },
  { ano_cerimonia: 1, categoria: 1, nome_do_filme: 1, vencedor: 1, _id: 0 }
).sort({ ano_cerimonia: 1 })

Viola Davis
# 3.5 Quantas vezes Viola Davis foi indicada ao Oscar?
R: 4
db.oscar.countDocuments({ nome_do_indicado: "Viola Davis" })

# 3.6 Quantos Oscars Viola Davis ganhou?
R: 1
db.oscar.countDocuments({ nome_do_indicado: "Viola Davis", vencedor: 1 })

# 3.7 Por quais filmes Viola Davis foi indicada?
R: ano_cerimonia: 2009,
  nome_do_filme: 'Doubt'
    ano_cerimonia: 2012,
  nome_do_filme: 'The Help'
  ano_cerimonia: 2017,
  nome_do_filme: 'Fences'
   ano_cerimonia: 2021,
  nome_do_filme: "Ma Rainey's Black Bottom"

  db.oscar.find(
  { nome_do_indicado: "Viola Davis" },
  { ano_cerimonia: 1, nome_do_filme: 1, _id: 0 }
).sort({ ano_cerimonia: 1 })


Amy Adams
# 3.8 Amy Adams já ganhou algum Oscar?
R: Não
db.oscar.countDocuments({ nome_do_indicado: "Amy Adams", vencedor: 1 })

# 3.9 Quantas vezes Amy Adams foi indicada sem ganhar?
R:6
db.oscar.countDocuments({ nome_do_indicado: "Amy Adams", vencedor: 0 })

Denzel Washington
# 3.10 Denzel Washington já ganhou algum Oscar?
R: sim, duas vezes
db.oscar.countDocuments({ nome_do_indicado: "Denzel Washington", vencedor: 1 })

# 3.11 Quantas vezes Denzel Washington foi indicado ao Oscar?
R: 9
db.oscar.countDocuments({ nome_do_indicado: "Denzel Washington" })


# 3.12 Liste todos os Oscars que Denzel Washington ganhou (ano, categoria, filme).
R:   ano_cerimonia: 1990,
  categoria: 'ACTOR IN A SUPPORTING ROLE',
  nome_do_filme: 'Glory'
   ano_cerimonia: 2002,
  categoria: 'ACTOR IN A LEADING ROLE',
  nome_do_filme: 'Training Day'

  db.oscar.find(
  { nome_do_indicado: "Denzel Washington", vencedor: 1 },
  { ano_cerimonia: 1, categoria: 1, nome_do_filme: 1, _id: 0 }
).sort({ ano_cerimonia: 1 })

Nível 4: Vencedores Históricos
# 4.1 Quem ganhou o primeiro Oscar para Melhor Atriz (ACTRESS)? Em que ano e por qual filme?
R:   ano_cerimonia: 1928,
  nome_do_indicado: 'Janet Gaynor',
  nome_do_filme: '7th Heaven'
  db.oscar.find(
  { categoria: "ACTRESS", vencedor: 1 },
  { ano_cerimonia: 1, nome_do_indicado: 1, nome_do_filme: 1, _id: 0 }
).sort({ ano_cerimonia: 1 }).limit(1)

# 4.2 Quem ganhou o primeiro Oscar para Melhor Ator (ACTOR)? Em que ano e por qual filme?
R:   ano_cerimonia: 1928,
  nome_do_indicado: 'Emil Jannings',
  nome_do_filme: 'The Last Command'

  db.oscar.find(
  { categoria: "ACTOR", vencedor: 1 },
  { ano_cerimonia: 1, nome_do_indicado: 1, nome_do_filme: 1, _id: 0 }
).sort({ ano_cerimonia: 1 }).limit(1)

# 4.3 Quantos vencedores existem ao todo na base de dados?
R: 2512
db.oscar.countDocuments({ vencedor: 1 })

# 4.4 Liste todos os filmes que ganharam o Oscar de Melhor Filme (categoria "OUTSTANDING PICTURE" ou "BEST PICTURE").
R:  ano_cerimonia: 1928,
  nome_do_filme: 'Wings'
   ano_cerimonia: 1929,
  nome_do_filme: 'The Broadway Melody'
  ano_cerimonia: 1963,
  nome_do_filme: 'Lawrence of Arabia'
   ano_cerimonia: 1964,
  nome_do_filme: 'Tom Jones'
   ano_cerimonia: 1965,
  nome_do_filme: 'My Fair Lady'
    ano_cerimonia: 1966,
  nome_do_filme: 'The Sound of Music'
   ano_cerimonia: 1967,
  nome_do_filme: 'A Man for All Seasons'
  ano_cerimonia: 1968,
  nome_do_filme: 'In the Heat of the Night'
    ano_cerimonia: 1969,
  nome_do_filme: 'Oliver!'
  ano_cerimonia: 1970,
  nome_do_filme: 'Midnight Cowboy'
   ano_cerimonia: 1971,
  nome_do_filme: 'Patton'
  ano_cerimonia: 1972,
  nome_do_filme: 'The French Connection'
  ano_cerimonia: 1973,
  nome_do_filme: 'The Godfather'
    ano_cerimonia: 1974,
  nome_do_filme: 'The Sting'
  ano_cerimonia: 1975,
  nome_do_filme: 'The Godfather Part II'
    ano_cerimonia: 1976,
  nome_do_filme: "One Flew over the Cuckoo's Nest"
    ano_cerimonia: 1977,
  nome_do_filme: 'Rocky'
  ano_cerimonia: 1978,
  nome_do_filme: 'Annie Hall'
   ano_cerimonia: 1979,
  nome_do_filme: 'The Deer Hunter'
    ano_cerimonia: 1980,
  nome_do_filme: 'Kramer vs. Kramer'
 
  db.oscar.find(
  { categoria: { $in: ["OUTSTANDING PICTURE", "BEST PICTURE"] }, vencedor: 1 },
  { ano_cerimonia: 1, nome_do_filme: 1, _id: 0 }
).sort({ ano_cerimonia: 1 })

# 4.5 Quantos filmes diferentes já ganharam o Oscar?
R: 1357
db.oscar.distinct("nome_do_filme", { vencedor: 1 }).length


