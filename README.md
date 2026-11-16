# Oscar-atividade
atividade do mongodb, perguntas e respostas estão Aqui
obs: no meu arquivo o nome está apenas oscar, por isso os comando ao invés de oscar_indicados está como oscar.

Nível 1: Primeiros Passos
Conhecendo a Base de Dados

# 1.1 Quantos registros existem na coleção de indicados ao Oscar?

R: 10889 registros

db.oscar.countDocuments();

#1.2 Quais são as diferentes categorias de premiação que existem no banco de dados? Liste todas as categorias únicas.

R: 115 registros

db.oscar.distinct("categoria");
#1.3 Qual foi o primeiro ano de cerimônia do Oscar registrado na base?

R: 1500

db.oscar.find().skip(15).limit(1).sort({ano_cerimonia: -1})
#1.4 Qual foi o último ano de cerimônia registrado na base?
R: 1928
db.oscar.find().skip(15).limit(1).sort({ano_cerimonia: 1})

# 1.5 Quantas cerimônias do Oscar estão registradas no total?
R: 97
db.oscar.distinct("ano_cerimonia").length

#1.6 Atualize os registros da coleção com os dados do Oscar 2025 e 2026 (pesquise os indicados e adicione-os).
(Indicados ao oscar adicionados ao documento)
R: 2025: 120 indicados; 2026: 125 indicados

db.oscar.countDocuments({ ano_cerimonia: 2025 })   // esperado: 120
db.oscar.countDocuments({ ano_cerimonia: 2026 })   // esperado: 125

##Nível 2: Explorando Categorias
#2.1 Quantas indicações existem para cada categoria? Agrupe por categoria e ordene da mais frequente para a menos frequente.
R:   directing: 479; film editing: 460; ACTOR IN A SUPPORTING ROLE: 450; ACTRESS IN A SUPPORTING ROLE: 450; best picture: 390; DOCUMENTARY (Short Subject): 378; CINEMATOGRAPHY: 348; DOCUMENTARY (Feature): 345; ART DIRECTION: 307; COSTUME DESIGN: 305; MUSIC (Original Score): 280; SOUND: 255; ACTOR IN A LEADING ROLE: 250; ACTRESS IN A LEADING ROLE: 250; MUSIC (Original Song): 245; SHORT FILM (Live Action):236; ACTRESS:236; ACTOR: 232; SHORT FILM (Animated):225.

db.oscar.aggregate([
  { $group: { _id: "$categoria", total: { $sum: 1 } } },
  { $sort: { total: -1 } }
])

#2.2 Qual categoria teve mais indicações ao longo da história do Oscar?
R: DIRECTING: 479
db.oscar.aggregate([
  { $group: { _id: "$categoria", total: { $sum: 1 } } },
  { $sort: { total: -1 } },
  { $limit: 1 }
])
#2.3 Qual categoria teve menos indicações ao longo da história?
R: SHORT FILM (Animated):225
db.oscar.aggregate([
  { $group: { _id: "$categoria", total: { $sum: 1 } } },
  { $sort: { total: 1 } },
  { $limit: 1 }
])

#2.4 A partir de que ano a categoria "ACTRESS" deixou de existir? (Dica: procure a última cerimônia com essa categoria)
R: 1976
db.oscar.find({ categoria: "ACTRESS" }).sort({ ano_cerimonia: -1 }).limit(1)

#2.5 Quais categorias existiam na primeira cerimônia (1928) e não existem mais hoje?
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
  
#2.6 Liste todas as categorias que contêm a palavra "DIRECTING" no nome.
R:  'DIRECTING',
  'DIRECTING (Comedy Picture)',
  'DIRECTING (Dramatic Picture)'

  db.oscar.distinct("categoria", { categoria: /DIRECTING/ })

  Nível 3: Atores e Atrizes Famosos
Natalie Portman

#3.1 Quantas vezes Natalie Portman foi indicada ao Oscar?

#3.2 Quantos Oscars Natalie Portman ganhou?

#3.3 Em quais anos e por quais filmes Natalie Portman foi indicada?

#3.4 Liste todas as indicações de Natalie Portman mostrando: ano, categoria, filme e se venceu.

Viola Davis
#3.5 Quantas vezes Viola Davis foi indicada ao Oscar?

#3.6 Quantos Oscars Viola Davis ganhou?

#3.7 Por quais filmes Viola Davis foi indicada?

Amy Adams
#3.8 Amy Adams já ganhou algum Oscar?

#3.9 Quantas vezes Amy Adams foi indicada sem ganhar?

Denzel Washington
#3.10 Denzel Washington já ganhou algum Oscar?

#3.11 Quantas vezes Denzel Washington foi indicado ao Oscar?

#3.12 Liste todos os Oscars que Denzel Washington ganhou (ano, categoria, filme).

