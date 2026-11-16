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

Nível 5: Análise de Indicações
# 5.1 Quais atores/atrizes foram indicados mais de uma vez? Liste o nome e o número de indicações.
R:  _id: 'Meryl Streep',
  indicacoes: 21
   _id: 'Katharine Hepburn',
  indicacoes: 12
   _id: 'Jack Nicholson',
  indicacoes: 12
    _id: 'Bette Davis',
  indicacoes: 11
    _id: 'Paul Newman',
  indicacoes: 9
   _id: 'Laurence Olivier',
  indicacoes: 9
   _id: 'Denzel Washington',
  indicacoes: 9
   _id: 'Al Pacino',
  indicacoes: 9
   _id: 'Spencer Tracy',
  indicacoes: 9
    _id: 'Cate Blanchett',
  indicacoes: 8
  _id: 'Glenn Close',
  indicacoes: 8
  _id: 'Jack Lemmon',
  indicacoes: 8
    _id: 'Judi Dench',
  indicacoes: 8
    _id: 'Geraldine Page',
  indicacoes: 8
  _id: 'Marlon Brando',
  indicacoes: 8
    _id: "Peter O'Toole",
  indicacoes: 8
    _id: 'Robert De Niro',
  indicacoes: 8
  _id: 'Leonardo DiCaprio',
  indicacoes: 7
    _id: 'Greer Garson',
  indicacoes: 7
    _id: 'Dustin Hoffman',
  indicacoes: 7

  db.oscar.aggregate([
  { $match: { categoria: /^ACT(OR|RESS)/ } },
  { $group: { _id: "$nome_do_indicado", indicacoes: { $sum: 1 } } },
  { $match: { indicacoes: { $gt: 1 } } },
  { $sort: { indicacoes: -1 } }
])

# 5.2 Qual ator ou atriz tem o maior número de indicações na história do Oscar?
R: _id: 'Meryl Streep',
  indicacoes: 21

  db.oscar.aggregate([
  { $match: { categoria: /^ACT(OR|RESS)/ } },
  { $group: { _id: "$nome_do_indicado", indicacoes: { $sum: 1 } } },
  { $sort: { indicacoes: -1 } },
  { $limit: 5 }
])

# 5.3 Quais atores foram indicados mais de 3 vezes, mas nunca ganharam?
R:  _id: "Peter O'Toole",
  indicacoes: 8,
  vitorias: 0
   _id: 'Richard Burton',
  indicacoes: 7,
  vitorias: 0
    _id: 'Albert Finney',
  indicacoes: 5,
  vitorias: 0
    _id: 'Bradley Cooper',
  indicacoes: 5,
  vitorias: 0
    _id: 'Arthur Kennedy',
  indicacoes: 5,
  vitorias: 0
    _id: 'Mickey Rooney',
  indicacoes: 4,
  vitorias: 0
    _id: 'Montgomery Clift',
  indicacoes: 4,
  vitorias: 0
   _id: 'Charles Boyer',
  indicacoes: 4,
  vitorias: 0
   _id: 'Mark Ruffalo',
  indicacoes: 4,
  vitorias: 0
   _id: 'Edward Norton',
  indicacoes: 4,
  vitorias: 0
  _id: 'Ed Harris',
  indicacoes: 4,
  vitorias: 0
   _id: 'Claude Rains',
  indicacoes: 4,
  vitorias: 0
   _id: 'Willem Dafoe',
  indicacoes: 4,
  vitorias: 0
   _id: 'Warren Beatty',
  indicacoes: 4,
  vitorias: 0

  db.oscar.aggregate([
  { $match: { categoria: /^ACTOR|^ACTOR IN/ } },
  { $group: { _id: "$nome_do_indicado", indicacoes: { $sum: 1 }, vitorias: { $sum: "$vencedor" } } },
  { $match: { indicacoes: { $gt: 3 }, vitorias: 0 } },
  { $sort: { indicacoes: -1 } }
])


# 5.4 Encontre todos os artistas que foram indicados em categorias diferentes (ex: ator e diretor).
R:  _id: 'Metro-Goldwyn-Mayer',
  categorias: [
    'SPECIAL EFFECTS',
    'BEST MOTION PICTURE',
    'DOCUMENTARY (Short Subject)',
    'UNIQUE AND ARTISTIC PICTURE',
    'OUTSTANDING MOTION PICTURE',
    'OUTSTANDING PICTURE',
    'SHORT SUBJECT (Two-reel)',
    'SHORT SUBJECT (One-reel)',
    'SHORT SUBJECT (Novelty)',
    'OUTSTANDING PRODUCTION',
    'SHORT SUBJECT (Cartoon)'
  ],
  qtd_categorias: 11

   _id: 'Sinners',
  categorias: [
    'WRITING (Original Screenplay)',
    'MAKEUP AND HAIRSTYLING',
    'COSTUME DESIGN',
    'BEST PICTURE',
    'CINEMATOGRAPHY',
    'SOUND',
    'PRODUCTION DESIGN',
    'VISUAL EFFECTS',
    'FILM EDITING',
    'CASTING'
  ],
  qtd_categorias: 10

    _id: 'Warner Bros.',
  categorias: [
    'SHORT SUBJECT (Color)',
    'BEST MOTION PICTURE',
    'DOCUMENTARY (Short Subject)',
    'SHORT SUBJECT (Comedy)',
    'SPECIAL AWARD',
    'OUTSTANDING MOTION PICTURE',
    'SHORT SUBJECT (Two-reel)',
    'SHORT SUBJECT (One-reel)',
    'OUTSTANDING PRODUCTION',
    'SHORT SUBJECT (Cartoon)'
  ],
  qtd_categorias: 10

    _id: 'Wicked',
  categorias: [
    'MAKEUP AND HAIRSTYLING',
    'COSTUME DESIGN',
    'SOUND',
    'BEST PICTURE',
    'PRODUCTION DESIGN',
    'VISUAL EFFECTS',
    'FILM EDITING',
    'MUSIC (Original Score)'
  ],
  qtd_categorias: 8

    _id: 'Paramount',
  categorias: [
    'SHORT SUBJECT (Cartoon)',
    'SHORT SUBJECT (Color)',
    'BEST MOTION PICTURE',
    'DOCUMENTARY (Short Subject)',
    'OUTSTANDING MOTION PICTURE',
    'SHORT SUBJECT (One-reel)',
    'OUTSTANDING PRODUCTION',
    'SPECIAL EFFECTS'
  ],
  qtd_categorias: 8

    _id: 'Emilia Pérez',
  categorias: [
    'MAKEUP AND HAIRSTYLING',
    'INTERNATIONAL FEATURE FILM',
    'SOUND',
    'CINEMATOGRAPHY',
    'WRITING (Adapted Screenplay)',
    'BEST PICTURE',
    'FILM EDITING',
    'MUSIC (Original Score)'
  ],
  qtd_categorias: 8

   _id: 'Frankenstein',
  categorias: [
    'COSTUME DESIGN',
    'SOUND',
    'CINEMATOGRAPHY',
    'BEST PICTURE',
    'PRODUCTION DESIGN',
    'WRITING (Adapted Screenplay)',
    'MAKEUP AND HAIRSTYLING'
  ],
  qtd_categorias: 7

   _id: 'RKO Radio',
  categorias: [
    'BEST MOTION PICTURE',
    'DOCUMENTARY (Short Subject)',
    'OUTSTANDING MOTION PICTURE',
    'SHORT SUBJECT (Comedy)',
    'SHORT SUBJECT (Two-reel)',
    'SHORT SUBJECT (One-reel)',
    'OUTSTANDING PRODUCTION'
  ],
  qtd_categorias: 7

   _id: 'Marty Supreme',
  categorias: [
    'COSTUME DESIGN',
    'WRITING (Original Screenplay)',
    'BEST PICTURE',
    'CINEMATOGRAPHY',
    'PRODUCTION DESIGN',
    'FILM EDITING',
    'CASTING'
  ],
  qtd_categorias: 7

    _id: 'Alex North',
  categorias: [
    'MUSIC (Original Music Score)',
    'MUSIC (Original Score--for a motion picture [not a musical])',
    'MUSIC (Music Score--substantially original)',
    'MUSIC (Original Dramatic Score)',
    'MUSIC (Music Score of a Dramatic or Comedy Picture)',
    'HONORARY AWARD',
    'MUSIC (Original Score)'
  ],
  qtd_categorias: 7

    _id: 'One Battle After Another',
  categorias: [
    'BEST PICTURE',
    'CINEMATOGRAPHY',
    'WRITING (Adapted Screenplay)',
    'SOUND',
    'PRODUCTION DESIGN',
    'FILM EDITING',
    'CASTING'
  ],
  qtd_categorias: 7

   _id: 'Walt Disney, Producer',
  categorias: [
    'DOCUMENTARY',
    'SHORT SUBJECT (Cartoon)',
    'SHORT SUBJECT (Live Action)',
    'DOCUMENTARY (Feature)',
    'DOCUMENTARY (Short Subject)',
    'SHORT SUBJECT (Two-reel)'
  ],
  qtd_categorias: 6

    _id: 'Alfred Newman',
  categorias: [
    'MUSIC (Scoring of a Musical Picture)',
    'MUSIC (Original Score)',
    'MUSIC (Music Score of a Dramatic Picture)',
    'MUSIC (Music Score--substantially original)',
    'MUSIC (Music Score of a Dramatic or Comedy Picture)',
    'MUSIC (Scoring)'
  ],
  qtd_categorias: 6

   _id: 'Conclave',
  categorias: [
    'COSTUME DESIGN',
    'PRODUCTION DESIGN',
    'WRITING (Adapted Screenplay)',
    'MUSIC (Original Score)',
    'FILM EDITING',
    'BEST PICTURE'
  ],
  qtd_categorias: 6

   _id: 'The Brutalist',
  categorias: [
    'FILM EDITING',
    'BEST PICTURE',
    'MUSIC (Original Score)',
    'WRITING (Original Screenplay)',
    'PRODUCTION DESIGN',
    'CINEMATOGRAPHY'
  ],
  qtd_categorias: 6

    _id: 'Ingmar Bergman',
  categorias: [
    'DIRECTING',
    'IRVING G. THALBERG MEMORIAL AWARD',
    'WRITING (Story and Screenplay--written directly for the screen)',
    'WRITING (Story and Screenplay--based on factual material or material not previously published or produced)',
    'WRITING (Screenplay Written Directly for the Screen)'
  ],
  qtd_categorias: 5

   _id: '20th Century-Fox',
  categorias: [
    'SHORT SUBJECT (Cartoon)',
    'OUTSTANDING PRODUCTION',
    'OUTSTANDING MOTION PICTURE',
    'SHORT SUBJECT (One-reel)',
    'BEST MOTION PICTURE'
  ],
  qtd_categorias: 5

    _id: 'John Williams',
  categorias: [
    'MUSIC (Original Dramatic Score)',
    'MUSIC (Original Score--for a motion picture [not a musical])',
    'MUSIC (Original Score)',
    'MUSIC (Original Musical or Comedy Score)',
    'MUSIC (Scoring of Music--adaptation or treatment)'
  ],
  qtd_categorias: 5

   _id: 'Jerry Goldsmith',
  categorias: [
    'MUSIC (Original Music Score)',
    'MUSIC (Original Score)',
    'MUSIC (Original Dramatic Score)',
    'MUSIC (Original Score--for a motion picture [not a musical])',
    'MUSIC (Music Score--substantially original)'
  ],
  qtd_categorias: 5

    _id: 'Robert Benton',
  categorias: [
    'WRITING (Screenplay Written Directly for the Screen--based on factual material or on story material not previously published or produced)',
    'WRITING (Screenplay Based on Material from Another Medium)',
    'WRITING (Screenplay Written Directly for the Screen)',
    'WRITING (Screenplay Based on Material Previously Produced or Published)',
    'DIRECTING'
  ],
  qtd_categorias: 5

  db.oscar.aggregate([
  { $group: { _id: "$nome_do_indicado", categorias: { $addToSet: "$categoria" } } },
  { $addFields: { qtd_categorias: { $size: "$categorias" } } },
  { $match: { qtd_categorias: { $gt: 1 } } },
  { $sort: { qtd_categorias: -1 } }
])



# 5.5 Quantos indicados têm exatamente 1 indicação na história?
R: 5688

db.oscar.aggregate([
  { $group: { _id: "$nome_do_indicado", total: { $sum: 1 } } },
  { $match: { total: 1 } },
  { $count: "indicados_com_1_indicacao" }
])

# 5.6 Qual o maior números de indicados em um único ano? Essa é uma pergunta franca.
R:  _id: 1943,
  total: 186

  db.oscar.aggregate([
  { $group: { _id: "$ano_cerimonia", total: { $sum: 1 } } },
  { $sort: { total: -1 } },
  { $limit: 3 }
])

Nível 6: Análise de Filmes
## Toy Story


# 6.1 A série de filmes Toy Story ganhou Oscars em quais anos?
R:2011 E 2020
db.oscar.distinct("ano_cerimonia", { nome_do_filme: /Toy Story/, vencedor: 1 })

# 6.2 Quantas indicações a franquia Toy Story recebeu no total?
R: 11
db.oscar.countDocuments({ nome_do_filme: /Toy Story/ })

# 6.3 Em quais categorias os filmes Toy Story foram indicados?
R: 'ANIMATED FEATURE FILM',
  'BEST PICTURE',
  'MUSIC (Original Musical or Comedy Score)',
  'MUSIC (Original Song)',
  'SOUND EDITING',
  'WRITING (Adapted Screenplay)',
  'WRITING (Screenplay Written Directly for the Screen)'

  db.oscar.distinct("categoria", { nome_do_filme: /Toy Story/ })

Crash

# 6.4 Em qual edição do Oscar o filme "Crash" concorreu?
R: 2006
db.oscar.distinct("ano_cerimonia", { nome_do_filme: /^Crash$/ })

# 6.5 Quantas indicações o filme "Crash" recebeu?
R: 6
db.oscar.countDocuments({ nome_do_filme: /^Crash$/ })

# 6.6 "Crash" ganhou o Oscar de Melhor Filme?
R:SIM
db.oscar.find({
  nome_do_filme: /^Crash$/,
  categoria: { $in: ["BEST PICTURE", "OUTSTANDING PICTURE"] },
  vencedor: 1
})

Central do Brasil

# 6.7 O filme "Central do Brasil" aparece no banco de dados?
R: SIM
db.oscar.find(
  { nome_do_filme: /Central (do Brasil|Station)/i },
  { ano_cerimonia: 1, nome_do_filme: 1, categoria: 1, nome_do_indicado: 1, vencedor: 1, _id: 0 }
)

# 6.8 Se sim, quantas indicações "Central do Brasil" recebeu?
R: 2
db.oscar.countDocuments({ nome_do_filme: /Central (do Brasil|Station)/i })

Nível 7: Operações de Atualização
# 7.1 No campo "vencedor", altere todos os valores "true" (string) para true (booleano) e "false" (string) para false (booleano).
R: db.oscar.aggregate([
  { $group: { _id: { $type: "$vencedor" }, total: { $sum: 1 } } }
])
# 7.2 Inclua no banco 3 filmes que nunca foram nomeados ao Oscar, mas que você acha que merecem. Use dados fictícios mas realistas.
R: const ultimo = db.oscar.find().sort({ id_registro: -1 }).limit(1).toArray()[0].id_registro;

db.oscar.insertMany([
  { id_registro: ultimo + 1, ano_filmagem: 2017, ano_cerimonia: 2018, cerimonia: 90,
    categoria: "BEST PICTURE", nome_do_indicado: "Paddington 2", nome_do_filme: "Paddington 2",
    vencedor: 0, ficticio: true },
  { id_registro: ultimo + 2, ano_filmagem: 1999, ano_cerimonia: 2000, cerimonia: 72,
    categoria: "ANIMATED FEATURE FILM", nome_do_indicado: "The Iron Giant", nome_do_filme: "The Iron Giant",
    vencedor: 0, ficticio: true },
  { id_registro: ultimo + 3, ano_filmagem: 2010, ano_cerimonia: 2011, cerimonia: 83,
    categoria: "FILM EDITING", nome_do_indicado: "Scott Pilgrim vs. the World", nome_do_filme: "Scott Pilgrim vs. the World",
    vencedor: 0, ficticio: true }
])

# 7.3 Adicione uma nova categoria chamada "BEST INTERNATIONAL FEATURE FILM" com alguns vencedores recentes (2020-2024).
R: const ultimo = db.oscar.find().sort({ id_registro: -1 }).limit(1).toArray()[0].id_registro;

db.oscar.insertMany([
  { id_registro: ultimo + 1, ano_filmagem: 2019, ano_cerimonia: 2020, cerimonia: 92,
    categoria: "BEST INTERNATIONAL FEATURE FILM", nome_do_indicado: "Bong Joon Ho", nome_do_filme: "Parasite", vencedor: 1 },
  { id_registro: ultimo + 2, ano_filmagem: 2020, ano_cerimonia: 2021, cerimonia: 93,
    categoria: "BEST INTERNATIONAL FEATURE FILM", nome_do_indicado: "Thomas Vinterberg", nome_do_filme: "Another Round", vencedor: 1 },
  { id_registro: ultimo + 3, ano_filmagem: 2021, ano_cerimonia: 2022, cerimonia: 94,
    categoria: "BEST INTERNATIONAL FEATURE FILM", nome_do_indicado: "Ryusuke Hamaguchi", nome_do_filme: "Drive My Car", vencedor: 1 },
  { id_registro: ultimo + 4, ano_filmagem: 2022, ano_cerimonia: 2023, cerimonia: 95,
    categoria: "BEST INTERNATIONAL FEATURE FILM", nome_do_indicado: "Edward Berger", nome_do_filme: "All Quiet on the Western Front", vencedor: 1 },
  { id_registro: ultimo + 5, ano_filmagem: 2023, ano_cerimonia: 2024, cerimonia: 96,
    categoria: "BEST INTERNATIONAL FEATURE FILM", nome_do_indicado: "Jonathan Glazer", nome_do_filme: "The Zone of Interest", vencedor: 1 }
])

# 7.4 Corrija possíveis erros de digitação nos nomes dos filmes (ex: espaços extras, caracteres especiais desnecessários).
R: db.oscar.countDocuments({ nome_do_filme: /^\s|\s$|\s{2,}/ })
db.oscar.updateMany(
  { nome_do_filme: /^\s|\s$/ },
  [{ $set: { nome_do_filme: { $trim: { input: "$nome_do_filme" } } } }]
)
db.oscar.updateMany(
  { nome_do_filme: /\s{2,}/ },
  [{ $set: { nome_do_filme: { $replaceAll: { input: "$nome_do_filme", find: "  ", replacement: " " } } } }]
)

# 7.5 Remova todos os registros com valor NULL no campo nome_do_filme.
R:db.oscar.deleteMany({ nome_do_filme: null })

Nível 8: Análise Temporal
# 8.1 Quantas indicações aconteceram por década? Agrupe por década (1920s, 1930s, etc.) e mostre o total.
R: _id: 1920,
  total: 72
   _id: 1930,
  total: 711
    _id: 1940,
  total: 1501
   _id: 1950,
  total: 1188
   _id: 1960,
  total: 1195
   _id: 1970,
  total: 1056
   _id: 1980,
  total: 1060
  _id: 1990,
  total: 1113
  _id: 2000,
  total: 1121
  _id: 2010,
  total: 1252
   _id: 2020,
  total: 872

db.oscar.aggregate([
  { $addFields: { ano_num: { $convert: { input: "$ano_cerimonia", to: "int", onError: null, onNull: null } } } },
  { $match: { ano_num: { $ne: null } } },
  { $group: {
      _id: { $subtract: ["$ano_num", { $mod: ["$ano_num", 10] }] },
      total: { $sum: 1 }
  } },
  { $sort: { _id: 1 } }
])




# 8.2 Em qual década houve o maior número de indicações?
R:   _id: 1940,
  total: 1501

  db.oscar.aggregate([
  { $addFields: { ano_num: { $convert: { input: "$ano_cerimonia", to: "int", onError: null, onNull: null } } } },
  { $match: { ano_num: { $ne: null } } },
  { $group: {
      _id: { $subtract: ["$ano_num", { $mod: ["$ano_num", 10] }] },
      total: { $sum: 1 }
  } },
  { $sort: { total: -1 } },
  { $limit: 3 }
])

# 8.3 Como o número de categorias evoluiu ao longo dos anos? Mostre quantas categorias únicas existiam a cada década.
R:_id: 1920,
  qtd_categorias: 15
  _id: 1930,
  qtd_categorias: 27
   _id: 1940,
  qtd_categorias: 38
   _id: 1950,
  qtd_categorias: 43
  _id: 1960,
  qtd_categorias: 40
    _id: 1970,
  qtd_categorias: 50
    _id: 1980,
  qtd_categorias: 34
  _id: 1990,
  qtd_categorias: 31
   _id: 2000,
  qtd_categorias: 31
    _id: 2010,
  qtd_categorias: 30
   _id: 2020,
  qtd_categorias: 33
  db.oscar.aggregate([
  ...prep,
  { $group: {
      _id: { $subtract: ["$ano_num", { $mod: ["$ano_num", 10] }] },
      categorias: { $addToSet: "$categoria" }
  } },
  { $project: { qtd_categorias: { $size: "$categorias" } } },
  { $sort: { _id: 1 } }
])

# 8.4 Qual foi o ano com o maior número de indicações registradas?
R:_id: 1943,
  total: 186
  
db.oscar.aggregate([
  ...prep,
  { $group: { _id: "$ano_num", total: { $sum: 1 } } },
  { $sort: { total: -1 } },
  { $limit: 3 }
])

# 8.5 Calcule a taxa de crescimento de indicações comparando a primeira década com a última.
R: primeira: 73,
  ultima: 872,
  crescimento_percentual: 1094.5205479452054

db.oscar.aggregate([
  ...prep,
  { $group: {
      _id: { $subtract: ["$ano_num", { $mod: ["$ano_num", 10] }] },
      total: { $sum: 1 }
  } },
  { $sort: { _id: 1 } },
  { $group: { _id: null, primeira: { $first: "$total" }, ultima: { $last: "$total" } } },
  { $project: {
      _id: 0, primeira: 1, ultima: 1,
      crescimento_percentual: {
        $multiply: [{ $divide: [{ $subtract: ["$ultima", "$primeira"] }, "$primeira"] }, 100]
      }
  } }
])

Nível 9: Questões Históricas e Sociais
Representatividade
# 9.1 Sidney Poitier foi o primeiro ator negro a ser indicado ao Oscar. Em que ano ele foi indicado? Por qual filme?
R: ano_cerimonia: 1959,
  categoria: 'ACTOR',
  nome_do_filme: 'The Defiant Ones',
  vencedor: 0

    ano_cerimonia: 1964,
  categoria: 'ACTOR',
  nome_do_filme: 'Lilies of the Field',
  vencedor: 1

  db.oscar.find(
  { nome_do_indicado: "Sidney Poitier" },
  { ano_cerimonia: 1, categoria: 1, nome_do_filme: 1, vencedor: 1, _id: 0 }
).sort({ ano_cerimonia: 1 })

# 9.2 Sidney Poitier ganhou o Oscar nessa indicação?
R: Na de 1964 Sim


# 9.3 Quantos atores/atrizes negros foram indicados na categoria ACTOR ou ACTRESS antes de 1970?
R: 6 Atores/atrizes


db.oscar.find(
  {
    nome_do_indicado: { $in: ["Hattie McDaniel", "Ethel Waters", "Dorothy Dandridge",
                              "Sidney Poitier", "Juanita Moore", "Beah Richards"] },
    categoria: /^ACT(OR|RESS)/,
    ano_cerimonia: { $lt: 1970 }
  },
  { ano_cerimonia: 1, nome_do_indicado: 1, categoria: 1, nome_do_filme: 1, _id: 0 }
).sort({ ano_cerimonia: 1 })

# 9.4 Liste todos os filmes dirigidos por mulheres que ganharam algum Oscar.
R:   "The Hurt Locker", "Nomadland", "The Piano", "Lost in Translation",
        "Promising Young Woman", "Women Talking", "The Substance", "Frozen",
        "Brave", "Barbie", "Little Women", "Yentl", "Zero Dark Thirty",
        "American Factory", "Citizenfour", "Free Solo", "Bao",
        "Period. End of Sentence.", "The Elephant Whisperers", "Encanto"

db.oscar.aggregate([
  { $match: {
      vencedor: 1,
      nome_do_filme: { $in: [
        "The Hurt Locker", "Nomadland", "The Piano", "Lost in Translation",
        "Promising Young Woman", "Women Talking", "The Substance", "Frozen",
        "Brave", "Barbie", "Little Women", "Yentl", "Zero Dark Thirty",
        "American Factory", "Citizenfour", "Free Solo", "Bao",
        "Period. End of Sentence.", "The Elephant Whisperers", "Encanto"
      ] }
  } },
  { $group: { _id: "$nome_do_filme", oscars: { $sum: 1 } } },
  { $sort: { oscars: -1 } }
])

Coincidências
# 9.5 Denzel Washington e Jamie Foxx já concorreram ao Oscar no mesmo ano?
R: Não

const denzel = db.oscar.distinct("ano_cerimonia", { nome_do_indicado: "Denzel Washington" });
const foxx = db.oscar.distinct("ano_cerimonia", { nome_do_indicado: "Jamie Foxx" });
denzel.filter(ano => foxx.includes(ano))

# 9.6 Se sim, em qual ano e quem ganhou?
R: Não concorreram o oscar no mesmo ano

# 9.7 Encontre casos onde o mesmo filme ganhou Oscar em múltiplas categorias na mesma cerimônia. Mostre o nome do filme e quantas categorias ele venceu.
R:  _id: {
    filme: 'Titanic',
    ano: 1998
  },
  categorias_vencidas: 11
    _id: {
    filme: 'The Lord of the Rings: The Return of the King',
    ano: 2004
  },
  categorias_vencidas: 11
    _id: {
    filme: 'Ben-Hur',
    ano: 1960
  },
  categorias_vencidas: 11
   _id: {
    filme: 'West Side Story',
    ano: 1962
  },
  categorias_vencidas: 10
    _id: {
    filme: 'The English Patient',
    ano: 1997
  },
  categorias_vencidas: 9
    _id: {
    filme: 'The Last Emperor',
    ano: 1988
  },
  categorias_vencidas: 9
  _id: {
    filme: 'Gigi',
    ano: 1959
  },
  categorias_vencidas: 9
  _id: {
    filme: 'Gandhi',
    ano: 1983
  },
  categorias_vencidas: 8
   _id: {
    filme: 'Gone with the Wind',
    ano: 1940
  },
  categorias_vencidas: 8
    _id: {
    filme: 'Slumdog Millionaire',
    ano: 2009
  },
  categorias_vencidas: 8
   _id: {
    filme: 'My Fair Lady',
    ano: 1965
  },
  categorias_vencidas: 8
  _id: {
    filme: 'Cabaret',
    ano: 1973
  },
  categorias_vencidas: 8
   _id: {
    filme: 'On the Waterfront',
    ano: 1955
  },
  categorias_vencidas: 8
   _id: {
    filme: 'Amadeus',
    ano: 1985
  },
  categorias_vencidas: 8
  _id: {
    filme: 'From Here to Eternity',
    ano: 1954
  },
  categorias_vencidas: 8
   _id: {
    filme: 'Out of Africa',
    ano: 1986
  },
  categorias_vencidas: 7
  _id: {
    filme: 'Gravity',
    ano: 2014
  },
  categorias_vencidas: 7
  _id: {
    filme: 'NULL',
    ano: 1933
  },
  categorias_vencidas: 7
  _id: {
    filme: 'Dances With Wolves',
    ano: 1991
  },
  categorias_vencidas: 7
    _id: {
    filme: 'Everything Everywhere All at Once',
    ano: 2023
  },
  categorias_vencidas: 7

db.oscar.aggregate([
  { $match: { vencedor: 1 } },
  { $group: {
      _id: { filme: "$nome_do_filme", ano: "$ano_cerimonia" },
      categorias_vencidas: { $sum: 1 }
  } },
  { $match: { categorias_vencidas: { $gt: 1 } } },
  { $sort: { categorias_vencidas: -1 } }
])


Nível 10: Análise Avançada
# 10.1 Quais filmes ganharam o Oscar de Melhor Filme ("OUTSTANDING PICTURE" ou "BEST PICTURE") e Melhor Diretor na mesma cerimônia?
R:   _id: {
    filme: 'All Quiet on the Western Front',
    ano: 1930
  },
  categorias: [
    'OUTSTANDING PRODUCTION',
    'DIRECTING'
      _id: {
    filme: 'Cavalcade',
    ano: 1933
  },
  categorias: [
    'OUTSTANDING PRODUCTION',
    'DIRECTING'
      _id: {
    filme: 'It Happened One Night',
    ano: 1935
  },
  categorias: [
    'DIRECTING',
    'OUTSTANDING PRODUCTION'

   _id: {
    filme: "You Can't Take It with You",
    ano: 1939
  },
  categorias: [
    'DIRECTING',
    'OUTSTANDING PRODUCTION'
    _id: {
    filme: 'Gone with the Wind',
    ano: 1940
  },
  categorias: [
    'OUTSTANDING PRODUCTION',
    'DIRECTING'
      _id: {
    filme: 'How Green Was My Valley',
    ano: 1942
  },
  categorias: [
    'OUTSTANDING MOTION PICTURE',
    'DIRECTING'
    _id: {
    filme: 'Mrs. Miniver',
    ano: 1943
  },
  categorias: [
    'OUTSTANDING MOTION PICTURE',
    'DIRECTING'
      _id: {
    filme: 'Casablanca',
    ano: 1944
  },
  categorias: [
    'OUTSTANDING MOTION PICTURE',
    'DIRECTING'
      _id: {
    filme: 'Going My Way',
    ano: 1945
  },
  categorias: [
    'BEST MOTION PICTURE',
    'DIRECTING'
      _id: {
    filme: 'The Lost Weekend',
    ano: 1946
  },
  categorias: [
    'BEST MOTION PICTURE',
    'DIRECTING'
     _id: {
    filme: 'The Best Years of Our Lives',
    ano: 1947
  },
  categorias: [
    'BEST MOTION PICTURE',
    'DIRECTING'
      _id: {
    filme: "Gentleman's Agreement",
    ano: 1948
  },
  categorias: [
    'DIRECTING',
    'BEST MOTION PICTURE'
     _id: {
    filme: 'All about Eve',
    ano: 1951
  },
  categorias: [
    'DIRECTING',
    'BEST MOTION PICTURE'
     _id: {
    filme: 'From Here to Eternity',
    ano: 1954
  },
  categorias: [
    'BEST MOTION PICTURE',
    'DIRECTING'
      _id: {
    filme: 'On the Waterfront',
    ano: 1955
  },
  categorias: [
    'DIRECTING',
    'BEST MOTION PICTURE'
      _id: {
    filme: 'Marty',
    ano: 1956
  },
  categorias: [
    'BEST MOTION PICTURE',
    'DIRECTING'
     _id: {
    filme: 'The Bridge on the River Kwai',
    ano: 1958
  },
  categorias: [
    'BEST MOTION PICTURE',
    'DIRECTING'
    _id: {
    filme: 'Gigi',
    ano: 1959
  },
  categorias: [
    'BEST MOTION PICTURE',
    'DIRECTING'
      _id: {
    filme: 'Ben-Hur',
    ano: 1960
  },
  categorias: [
    'DIRECTING',
    'BEST MOTION PICTURE'
      _id: {
    filme: 'The Apartment',
    ano: 1961
  },
  categorias: [
    'BEST MOTION PICTURE',
    'DIRECTING'

db.oscar.aggregate([
  { $match: { vencedor: 1, categoria: { $in: [...pic, ...dir] } } },
  { $group: {
      _id: { filme: "$nome_do_filme", ano: "$ano_cerimonia" },
      categorias: { $addToSet: "$categoria" }
  } },
  { $match: { $expr: { $and: [
      { $gt: [{ $size: { $setIntersection: ["$categorias", pic] } }, 0] },
      { $gt: [{ $size: { $setIntersection: ["$categorias", dir] } }, 0] }
  ] } } },
  { $sort: { "_id.ano": 1 } }
])

# 10.2 Qual filme recebeu o maior número de indicações em uma única cerimônia?
R:   _id: {
    filme: 'NULL',
    ano: 1933
  },
  indicacoes: 18
  db.oscar.aggregate([
  { $group: {
      _id: { filme: "$nome_do_filme", ano: "$ano_cerimonia" },
      indicacoes: { $sum: 1 }
  } },
  { $sort: { indicacoes: -1 } },
  { $limit: 5 }
])

# 10.3 Qual filme teve a maior taxa de conversão (porcentagem de indicações que viraram vitórias)?
R:   _id: {
    filme: 'The Lord of the Rings: The Return of the King',
    ano: 2004
  },
  indicacoes: 11,
  vitorias: 11,
  taxa_percentual: 100

  db.oscar.aggregate([
  { $group: {
      _id: { filme: "$nome_do_filme", ano: "$ano_cerimonia" },
      indicacoes: { $sum: 1 },
      vitorias: { $sum: { $cond: [{ $in: ["$vencedor", [1, true]] }, 1, 0] } }
  } },
  { $match: { indicacoes: { $gte: 3 } } },
  { $addFields: { taxa_percentual: { $multiply: [{ $divide: ["$vitorias", "$indicacoes"] }, 100] } } },
  { $sort: { taxa_percentual: -1, indicacoes: -1 } },
  { $limit: 10 }
])

# 10.4 Encontre atores que foram indicados em anos consecutivos. Liste o nome e os anos.
R:   _id: 'Adam Driver',
  anos: [
    2019,
    2020
     _id: 'Al Pacino',
  anos: [
    1973,
    1974,
    1975,
    1976,
    1980,
    1991,
    1993,
    2020
    _id: 'Albert Finney',
  anos: [
    1964,
    1975,
    1984,
    1985,
    2001
     _id: 'Alice Brady',
  anos: [
    1937,
    1938
      _id: 'Amy Adams',
  anos: [
    2006,
    2009,
    2011,
    2013,
    2014,
    2019
      _id: 'Angela Lansbury',
  anos: [
    1945,
    1946,
    1963
      _id: 'Anjelica Huston',
  anos: [
    1986,
    1990,
    1991
      _id: 'Anthony Hopkins',
  anos: [
    1992,
    1994,
    1996,
    1998,
    2020,
    2021
      _id: 'Anthony Quinn',
  anos: [
    1953,
    1957,
    1958,
    1965
     _id: 'Arthur Kennedy',
  anos: [
    1950,
    1952,
    1956,
    1958,
    1959
      _id: 'Audrey Hepburn',
  anos: [
    1954,
    1955,
    1960,
    1962,
    1968
      _id: 'Bette Davis',
  anos: [
    1935,
    1936,
    1939,
    1940,
    1941,
    1942,
    1943,
    1945,
    1951,
    1953,
    1963
      _id: 'Bing Crosby',
  anos: [
    1945,
    1946,
    1955
     _id: 'Bradley Cooper',
  anos: [
    2013,
    2014,
    2015,
    2019,
    2024
     _id: 'Burgess Meredith',
  anos: [
    1976,
    1977
     _id: 'Cate Blanchett',
  anos: [
    1999,
    2005,
    2007,
    2008,
    2014,
    2016,
    2023
     _id: 'Celeste Holm',
  anos: [
    1948,
    1950,
    1951
      _id: 'Charles Bickford',
  anos: [
    1944,
    1948,
    1949
      _id: 'Charles Boyer',
  anos: [
    1938,
    1939,
    1945,
    1962
      _id: 'Charles Durning',
  anos: [
    1983,
    1984


db.oscar.aggregate([
  { $match: { categoria: /^ACT(OR|RESS)/ } },
  { $group: { _id: "$nome_do_indicado", anos: { $addToSet: "$ano_cerimonia" } } },
  { $addFields: { anos: { $sortArray: { input: "$anos", sortBy: 1 } } } },
  { $addFields: { consecutivos: { $filter: {
      input: "$anos", as: "a",
      cond: { $in: [{ $add: ["$$a", 1] }, "$anos"] }
  } } } },
  { $match: { "consecutivos.0": { $exists: true } } },
  { $project: { anos: 1 } },
  { $sort: { _id: 1 } }
])

# 10.5 Qual a média de indicações por cerimônia ao longo da história?
R:90
db.oscar.aggregate([
  { $group: { _id: "$cerimonia", total: { $sum: 1 } } },
  { $group: { _id: null, media: { $avg: "$total" }, cerimonias: { $sum: 1 } } }
])

# 10.6 Identifique "surpresas" - indicados em categorias principais (ACTOR, ACTRESS, BEST PICTURE) cujo filme só teve uma indicação.
R: ano_cerimonia: 1928,
  categoria: 'ACTRESS',
  nome_do_indicado: 'Louise Dresser',
  nome_do_filme: 'A Ship Comes In',
  vencedor: 0
    ano_cerimonia: 1928,
  categoria: 'OUTSTANDING PICTURE',
  nome_do_indicado: 'The Caddo Company',
  nome_do_filme: 'The Racket',
  vencedor: 0
    nome_do_indicado: 'Gloria Swanson',
  nome_do_filme: 'Sadie Thompson',
  vencedor: 0,
  ano_cerimonia: 1928,
  categoria: 'ACTRESS'
    ano_cerimonia: 1928,
  categoria: 'ACTOR',
  nome_do_indicado: 'Richard Barthelmess',
  nome_do_filme: 'The Noose',
  vencedor: 0

 ano_cerimonia: 1928,
  categoria: 'UNIQUE AND ARTISTIC PICTURE',
  nome_do_indicado: 'Paramount Famous Lasky',
  nome_do_filme: 'Chang',
  vencedor: 0
   ano_cerimonia: 1929,
  categoria: 'ACTRESS',
  nome_do_indicado: 'Jeanne Eagels',
  nome_do_filme: 'The Letter',
  vencedor: 0
    ano_cerimonia: 1929,
  categoria: 'ACTRESS',
  nome_do_indicado: 'Mary Pickford',
  nome_do_filme: 'Coquette',
  vencedor: 1
   ano_cerimonia: 1929,
  categoria: 'ACTRESS',
  nome_do_indicado: 'Betty Compson',
  nome_do_filme: 'The Barker',
  vencedor: 0
   ano_cerimonia: 1929,
  categoria: 'ACTOR',
  nome_do_indicado: 'George Bancroft',
  nome_do_filme: 'Thunderbolt',
  vencedor: 0
    ano_cerimonia: 1929,
  categoria: 'OUTSTANDING PICTURE',
  nome_do_indicado: 'Metro-Goldwyn-Mayer',
  nome_do_filme: 'Hollywood Revue',
  vencedor: 0
   ano_cerimonia: 1929,
  categoria: 'ACTOR',
  nome_do_indicado: 'Paul Muni',
  nome_do_filme: 'The Valiant',
  vencedor: 0
   ano_cerimonia: 1930,
  categoria: 'ACTOR',
  nome_do_indicado: 'Lawrence Tibbett',
  nome_do_filme: 'The Rogue Song',
  vencedor: 0
   ano_cerimonia: 1930,
  categoria: 'ACTOR',
  nome_do_indicado: 'George Arliss',
  nome_do_filme: 'The Green Goddess',
  vencedor: 0
   ano_cerimonia: 1930,
  categoria: 'ACTRESS',
  nome_do_indicado: 'Gloria Swanson',
  nome_do_filme: 'The Trespasser',
  vencedor: 0
   ano_cerimonia: 1930,
  categoria: 'ACTRESS',
  nome_do_indicado: 'Ruth Chatterton',
  nome_do_filme: 'Sarah and Son',
  vencedor: 0
  ano_cerimonia: 1930,
  categoria: 'ACTRESS',
  nome_do_indicado: 'Nancy Carroll',
  nome_do_filme: "The Devil's Holiday",
  vencedor: 0
    ano_cerimonia: 1930,
  categoria: 'ACTRESS',
  nome_do_indicado: 'Norma Shearer',
  nome_do_filme: 'Their Own Desire',
  vencedor: 0
   ano_cerimonia: 1930,
  categoria: 'ACTOR',
  nome_do_indicado: 'Maurice Chevalier',
  nome_do_filme: 'The Big Pond',
  vencedor: 0
    ano_cerimonia: 1931,
  categoria: 'ACTOR',
  nome_do_indicado: 'Fredric March',
  nome_do_filme: 'The Royal Family of Broadway',
  vencedor: 0
    ano_cerimonia: 1931,
  categoria: 'OUTSTANDING PRODUCTION',
  nome_do_indicado: 'Metro-Goldwyn-Mayer',
  nome_do_filme: 'Trader Horn',
  vencedor: 0
  


db.oscar.aggregate([
  { $match: { ficticio: { $ne: true } } },
  { $group: {
      _id: { filme: "$nome_do_filme", ano: "$ano_cerimonia" },
      total: { $sum: 1 },
      docs: { $push: "$$ROOT" }
  } },
  { $match: { total: 1 } },
  { $unwind: "$docs" },
  { $replaceRoot: { newRoot: "$docs" } },
  { $match: { $or: [
      { categoria: /^(ACTOR|ACTRESS)( IN A LEADING ROLE)?$/ },
      { categoria: { $in: pic } }
  ] } },
  { $project: { ano_cerimonia: 1, categoria: 1, nome_do_indicado: 1, nome_do_filme: 1, vencedor: 1, _id: 0 } },
  { $sort: { ano_cerimonia: 1 } }
])

Nível 11: Desafios Complexos
# 11.1 Crie um ranking dos 10 filmes mais premiados da história (que ganharam mais Oscars).
R: _id: {
    filme: 'Ben-Hur',
    ano: 1960
  },
  oscars: 11
}
 _id: {
    filme: 'The Lord of the Rings: The Return of the King',
    ano: 2004
  },
  oscars: 11
    _id: {
    filme: 'Titanic',
    ano: 1998
  },
  oscars: 11
   _id: {
    filme: 'West Side Story',
    ano: 1962
  },
  oscars: 10
    _id: {
    filme: 'Gigi',
    ano: 1959
  },
  oscars: 9
}
  _id: {
    filme: 'The English Patient',
    ano: 1997
  },
  oscars: 9
    _id: {
    filme: 'The Last Emperor',
    ano: 1988
  },
  oscars: 9
    _id: {
    filme: 'Slumdog Millionaire',
    ano: 2009
  },
  oscars: 8
  _id: {
    filme: 'Amadeus',
    ano: 1985
  },
  oscars: 8
    _id: {
    filme: 'Gone with the Wind',
    ano: 1940
  },
  oscars: 8

  db.oscar.aggregate([
  real,
  { $match: { vencedor: venceu, nome_do_filme: { $ne: null } } },
  { $group: { _id: { filme: "$nome_do_filme", ano: "$ano_cerimonia" }, oscars: { $sum: 1 } } },
  { $sort: { oscars: -1 } },
  { $limit: 12 }
])

# 11.2 Crie um ranking dos 10 artistas (atores/diretores) mais indicados da história, independente da categoria.
R:   _id: 'John Williams',
  indicacoes: 46
   _id: 'Alfred Newman',
  indicacoes: 34
    _id: 'Gordon Hollingshead, Producer',
  indicacoes: 22
  _id: 'Meryl Streep',
  indicacoes: 21
    _id: 'Victor Young',
  indicacoes: 19
   _id: 'Woody Allen',
  indicacoes: 18
  _id: 'Leon Shamroy',
  indicacoes: 17
   _id: 'Pete Smith, Producer',
  indicacoes: 16
  _id: 'Max Steiner',
  indicacoes: 16
    _id: 'Pete Smith, Producer',
  indicacoes: 16
  db.oscar.aggregate([
  real,
  { $match: { nome_do_indicado: { $ne: null } } },
  { $group: { _id: "$nome_do_indicado", indicacoes: { $sum: 1 } } },
  { $sort: { indicacoes: -1 } },
  { $limit: 25 }
])

# 11.3 Encontre "azarões" - artistas com mais de 5 indicações e 0 vitórias.
R:   _id: 'Thomas Newman',
  indicacoes: 14,
  vitorias: 0
   _id: 'George Folsey',
  indicacoes: 12,
  vitorias: 0
   _id: 'Walter Lantz, Producer',
  indicacoes: 10,
  vitorias: 0
    _id: 'Randy Newman',
  indicacoes: 9,
  vitorias: 0
    _id: "Peter O'Toole",
  indicacoes: 8,
  vitorias: 0
  _id: 'Glenn Close',
  indicacoes: 8,
  vitorias: 0
  

db.oscar.aggregate([
  real,
  { $match: { nome_do_indicado: { $ne: null } } },
  { $group: {
      _id: "$nome_do_indicado",
      indicacoes: { $sum: 1 },
      vitorias: { $sum: { $cond: [{ $in: ["$vencedor", [1, true]] }, 1, 0] } }
  } },
  { $match: { indicacoes: { $gt: 5 }, vitorias: 0 } },
  { $sort: { indicacoes: -1 } }
])

# 11.4 Qual categoria tem a maior concentração de vitórias (menos vencedores diferentes ao longo do tempo)?
R: _id: 'FOREIGN LANGUAGE FILM',
  vitorias: 63,
  vencedores_diferentes: 28,
  razao: 0.4444444444444444

db.oscar.aggregate([
  real,
  { $match: { vencedor: venceu } },
  { $group: { _id: "$categoria", vitorias: { $sum: 1 }, vencedores: { $addToSet: "$nome_do_indicado" } } },
  { $project: {
      vitorias: 1,
      vencedores_diferentes: { $size: "$vencedores" },
      razao: { $divide: [{ $size: "$vencedores" }, "$vitorias"] }
  } },
  { $match: { vitorias: { $gte: 10 } } },
  { $sort: { razao: 1 } },
  { $limit: 10 }
])

# 11.5 Calcule a "competitividade" de cada categoria (média de indicados por cerimônia).
R:  _id: 'DOCUMENTARY',
  media_indicados: 25,
  cerimonias: 1
    _id: 'MUSIC (Music Score of a Dramatic Picture)',
  media_indicados: 20,
  cerimonias: 1
    _id: 'OUTSTANDING MOTION PICTURE',
  media_indicados: 10,
  cerimonias: 3
  _id: 'OUTSTANDING PRODUCTION',
  media_indicados: 9.272727272727273,
  cerimonias: 11
    _id: 'MUSIC (Scoring)',
  media_indicados: 8,
  cerimonias: 8
    _id: 'MUSIC (Music Score of a Dramatic or Comedy Picture)',
  media_indicados: 7.7894736842105265,
  cerimonias: 19
    _id: 'DANCE DIRECTION',
  media_indicados: 7,
  cerimonias: 3
   _id: 'ASSISTANT DIRECTOR',
  media_indicados: 7,
  cerimonias: 5
   _id: 'SOUND RECORDING',
  media_indicados: 6.964285714285714,
  cerimonias: 28
   _id: 'MUSIC (Scoring of a Musical Picture)',
  media_indicados: 6.35,
  cerimonias: 20
    _id: 'BEST PICTURE',
  media_indicados: 6.109375,
  cerimonias: 64
  _id: 'MUSIC (Song)',
  media_indicados: 5.972222222222222,
  cerimonias: 36
   _id: 'CINEMATOGRAPHY (Black-and-White)',
  media_indicados: 5.962962962962963,
  cerimonias: 27
   _id: 'ART DIRECTION (Black-and-White)',
  media_indicados: 5.52,
  cerimonias: 25
    _id: 'WRITING',
  media_indicados: 5.5,
  cerimonias: 2
  _id: 'MUSIC (Original Score)',
  media_indicados: 5.490196078431373,
  cerimonias: 51
   _id: 'ART DIRECTION',
  media_indicados: 5.203389830508475,
  cerimonias: 59
  _id: 'MUSIC (Original Dramatic Score)',
  media_indicados: 5.125,
  cerimonias: 8
    _id: 'MUSIC (Score of a Musical Picture--original or adaptation)',
  media_indicados: 5,
  cerimonias: 2
    _id: 'MUSIC (Original Music Score)',
  media_indicados: 5,
  cerimonias: 2

db.oscar.aggregate([
  real,
  { $group: { _id: { categoria: "$categoria", cerimonia: "$cerimonia" }, indicados: { $sum: 1 } } },
  { $group: { _id: "$_id.categoria", media_indicados: { $avg: "$indicados" }, cerimonias: { $sum: 1 } } },
  { $sort: { media_indicados: -1 } }
])

# 11.6 Encontre filmes que foram indicados em uma categoria em um ano e ganharam em outra categoria em outro ano.
R:id: 'Cyrano de Bergerac',
  perdidas: [
    {
      categoria: 'FOREIGN LANGUAGE FILM',
      ano: 1991
    },
    {
      categoria: 'MAKEUP',
      ano: 1991
    },
    {
      categoria: 'ACTOR IN A LEADING ROLE',
      ano: 1991
    },
    {
      categoria: 'ART DIRECTION',
      ano: 1991
    }
  ],
  vitorias: [
    {
      categoria: 'ACTOR',
      ano: 1951
    },
    {
      categoria: 'COSTUME DESIGN',
      ano: 1991
    }
  ]
}

_id: 'War and Peace',
  perdidas: [
    {
      categoria: 'COSTUME DESIGN (Color)',
      ano: 1957
    },
    {
      categoria: 'DIRECTING',
      ano: 1957
    },
    {
      categoria: 'CINEMATOGRAPHY (Color)',
      ano: 1957
    },
    {
      categoria: 'ART DIRECTION',
      ano: 1969
    }
  ],
  vitorias: [
    {
      categoria: 'FOREIGN LANGUAGE FILM',
      ano: 1969
    }
  ]
  _id: 'A Star Is Born',
  perdidas: [
    {
      categoria: 'OUTSTANDING PRODUCTION',
      ano: 1938
    },
    {
      categoria: 'ASSISTANT DIRECTOR',
      ano: 1938
    },
    {
      categoria: 'DIRECTING',
      ano: 1938
    },
    {
      categoria: 'WRITING (Screenplay)',
      ano: 1938
    },
    {
      categoria: 'ACTOR',
      ano: 1938
    },
    {
      categoria: 'ACTRESS',
      ano: 1938
    },
    {
      categoria: 'COSTUME DESIGN (Color)',
      ano: 1955
    },
    {
      categoria: 'MUSIC (Song)',
      ano: 1955
    },
    {
      categoria: 'ACTRESS',
      ano: 1955
    },
    {
      categoria: 'ART DIRECTION (Color)',
      ano: 1955
    },
    {
      categoria: 'ACTOR',
      ano: 1955
    },
    {
      categoria: 'MUSIC (Scoring of a Musical Picture)',
      ano: 1955
    },
    {
      categoria: 'CINEMATOGRAPHY',
      ano: 1977
    },
    {
      categoria: 'MUSIC (Original Song Score and Its Adaptation or Adaptation Score)',
      ano: 1977
    },
    {
      categoria: 'SOUND',
      ano: 1977
    },
    {
      categoria: 'ACTOR IN A SUPPORTING ROLE',
      ano: 2019
    },
    {
      categoria: 'WRITING (Adapted Screenplay)',
      ano: 2019
    },
    {
      categoria: 'CINEMATOGRAPHY',
      ano: 2019
    },
    {
      categoria: 'ACTOR IN A LEADING ROLE',
      ano: 2019
    },
    {
      categoria: 'ACTRESS IN A LEADING ROLE',
      ano: 2019
    },
    {
      categoria: 'BEST PICTURE',
      ano: 2019
    },
    {
      categoria: 'SOUND MIXING',
      ano: 2019
    }
  ],
  vitorias: [
    {
      categoria: 'WRITING (Original Story)',
      ano: 1938
    },
    {
      categoria: 'MUSIC (Original Song)',
      ano: 1977
    },
    {
      categoria: 'MUSIC (Original Song)',
      ano: 2019
    }
  ]
}
_id: 'Henry V',
  perdidas: [
    {
      categoria: 'ACTOR',
      ano: 1947
    },
    {
      categoria: 'ART DIRECTION (Color)',
      ano: 1947
    },
    {
      categoria: 'MUSIC (Music Score of a Dramatic or Comedy Picture)',
      ano: 1947
    },
    {
      categoria: 'BEST MOTION PICTURE',
      ano: 1947
    },
    {
      categoria: 'ACTOR IN A LEADING ROLE',
      ano: 1990
    },
    {
      categoria: 'DIRECTING',
      ano: 1990
    }
  ],
  vitorias: [
    {
      categoria: 'COSTUME DESIGN',
      ano: 1990
    }
  ]
  _id: 'Charade',
  perdidas: [
    {
      categoria: 'MUSIC (Song)',
      ano: 1964
    }
  ],
  vitorias: [
    {
      categoria: 'SHORT FILM (Animated)',
      ano: 1985
    }
  ]
}
_id: 'The Hurricane',
  perdidas: [
    {
      categoria: 'ACTOR IN A SUPPORTING ROLE',
      ano: 1938
    },
    {
      categoria: 'MUSIC (Scoring)',
      ano: 1938
    },
    {
      categoria: 'ACTOR IN A LEADING ROLE',
      ano: 2000
    }
  ],
  vitorias: [
    {
      categoria: 'SOUND RECORDING',
      ano: 1938
    }
  ]
}
_id: 'The Shop on Main Street',
  perdidas: [
    {
      categoria: 'ACTRESS',
      ano: 1967
    }
  ],
  vitorias: [
    {
      categoria: 'FOREIGN LANGUAGE FILM',
      ano: 1966
    }
  ]
}
 _id: 'Les Misérables',
  perdidas: [
    {
      categoria: 'PRODUCTION DESIGN',
      ano: 2013
    },
    {
      categoria: 'COSTUME DESIGN',
      ano: 2013
    },
    {
      categoria: 'MUSIC (Original Song)',
      ano: 2013
    },
    {
      categoria: 'ACTOR IN A LEADING ROLE',
      ano: 2013
    },
    {
      categoria: 'BEST PICTURE',
      ano: 2013
    },
    {
      categoria: 'INTERNATIONAL FEATURE FILM',
      ano: 2020
    }
  ],
  vitorias: [
    {
      categoria: 'ACTRESS IN A SUPPORTING ROLE',
      ano: 2013
    },
    {
      categoria: 'MAKEUP AND HAIRSTYLING',
      ano: 2013
    },
    {
      categoria: 'SOUND MIXING',
      ano: 2013
    }
  ]
}
 _id: 'Grand Canyon',
  perdidas: [
    {
      categoria: 'WRITING (Screenplay Written Directly for the Screen)',
      ano: 1992
    }
  ],
  vitorias: [
    {
      categoria: 'SHORT SUBJECT (Live Action)',
      ano: 1959
    }
  ]
}
_id: 'The Time Machine',
  perdidas: [
    {
      categoria: 'MAKEUP',
      ano: 2003
    }
  ],
  vitorias: [
    {
      categoria: 'SPECIAL EFFECTS',
      ano: 1961
    }
  ]
}
_id: 'Beauty and the Beast',
  perdidas: [
    {
      categoria: 'SOUND',
      ano: 1992
    },
    {
      categoria: 'MUSIC (Original Song)',
      ano: 1992
    },
    {
      categoria: 'BEST PICTURE',
      ano: 1992
    },
    {
      categoria: 'MUSIC (Original Song)',
      ano: 1992
    },
    {
      categoria: 'COSTUME DESIGN',
      ano: 2018
    },
    {
      categoria: 'PRODUCTION DESIGN',
      ano: 2018
    }
  ],
  vitorias: [
    {
      categoria: 'MUSIC (Original Song)',
      ano: 1992
    },
    {
      categoria: 'MUSIC (Original Score)',
      ano: 1992
    }
  ]
}
 _id: 'Pinocchio',
  perdidas: [
    {
      categoria: 'MAKEUP AND HAIRSTYLING',
      ano: 2021
    },
    {
      categoria: 'COSTUME DESIGN',
      ano: 2021
    }
  ],
  vitorias: [
    {
      categoria: 'MUSIC (Original Score)',
      ano: 1941
    },
    {
      categoria: 'MUSIC (Song)',
      ano: 1941
    }
  ]
}
_id: 'King Kong',
  perdidas: [
    {
      categoria: 'CINEMATOGRAPHY',
      ano: 1977
    },
    {
      categoria: 'SOUND',
      ano: 1977
    },
    {
      categoria: 'ART DIRECTION',
      ano: 2006
    }
  ],
  vitorias: [
    {
      categoria: 'SPECIAL ACHIEVEMENT AWARD (Visual Effects)',
      ano: 1977
    },
    {
      categoria: 'VISUAL EFFECTS',
      ano: 2006
    },
    {
      categoria: 'SOUND EDITING',
      ano: 2006
    },
    {
      categoria: 'SOUND MIXING',
      ano: 2006
    }
  ]
}
_id: 'Hamlet',
  perdidas: [
    {
      categoria: 'MUSIC (Music Score of a Dramatic or Comedy Picture)',
      ano: 1949
    },
    {
      categoria: 'ACTRESS IN A SUPPORTING ROLE',
      ano: 1949
    },
    {
      categoria: 'DIRECTING',
      ano: 1949
    },
    {
      categoria: 'ART DIRECTION',
      ano: 1991
    },
    {
      categoria: 'COSTUME DESIGN',
      ano: 1991
    },
    {
      categoria: 'WRITING (Screenplay Based on Material Previously Produced or Published)',
      ano: 1997
    },
    {
      categoria: 'MUSIC (Original Dramatic Score)',
      ano: 1997
    },
    {
      categoria: 'ART DIRECTION',
      ano: 1997
    },
    {
      categoria: 'COSTUME DESIGN',
      ano: 1997
    }
  ],
  vitorias: [
    {
      categoria: 'COSTUME DESIGN (Black-and-White)',
      ano: 1949
    },
    {
      categoria: 'ART DIRECTION (Black-and-White)',
      ano: 1949
    },
    {
      categoria: 'ACTOR',
      ano: 1949
    },
    {
      categoria: 'BEST MOTION PICTURE',
      ano: 1949
    }
  ]
}
_id: 'Sabrina',
  perdidas: [
    {
      categoria: 'CINEMATOGRAPHY (Black-and-White)',
      ano: 1955
    },
    {
      categoria: 'DIRECTING',
      ano: 1955
    },
    {
      categoria: 'ACTRESS',
      ano: 1955
    },
    {
      categoria: 'ART DIRECTION (Black-and-White)',
      ano: 1955
    },
    {
      categoria: 'WRITING (Screenplay)',
      ano: 1955
    },
    {
      categoria: 'MUSIC (Original Song)',
      ano: 1996
    },
    {
      categoria: 'MUSIC (Original Musical or Comedy Score)',
      ano: 1996
    }
  ],
  vitorias: [
    {
      categoria: 'COSTUME DESIGN (Black-and-White)',
      ano: 1955
    }
  ]
}
_id: 'A Place in the Sun',
  perdidas: [
    {
      categoria: 'BEST MOTION PICTURE',
      ano: 1952
    },
    {
      categoria: 'ACTOR',
      ano: 1952
    },
    {
      categoria: 'ACTRESS',
      ano: 1952
    },
    {
      categoria: 'SHORT SUBJECT (Cartoon)',
      ano: 1961
    }
  ],
  vitorias: [
    {
      categoria: 'MUSIC (Music Score of a Dramatic or Comedy Picture)',
      ano: 1952
    },
    {
      categoria: 'WRITING (Screenplay)',
      ano: 1952
    },
    {
      categoria: 'COSTUME DESIGN (Black-and-White)',
      ano: 1952
    },
    {
      categoria: 'DIRECTING',
      ano: 1952
    },
    {
      categoria: 'FILM EDITING',
      ano: 1952
    },
    {
      categoria: 'CINEMATOGRAPHY (Black-and-White)',
      ano: 1952
    }
  ]
}
_id: 'The Champ',
  perdidas: [
    {
      categoria: 'OUTSTANDING PRODUCTION',
      ano: 1932
    },
    {
      categoria: 'DIRECTING',
      ano: 1932
    },
    {
      categoria: 'MUSIC (Original Score)',
      ano: 1980
    }
  ],
  vitorias: [
    {
      categoria: 'WRITING (Original Story)',
      ano: 1932
    },
    {
      categoria: 'ACTOR',
      ano: 1932
    }
  ]
}
 _id: 'The Last of the Mohicans',
  perdidas: [
    {
      categoria: 'ASSISTANT DIRECTOR',
      ano: 1937
    }
  ],
  vitorias: [
    {
      categoria: 'SOUND',
      ano: 1993
    }
  ]
}
_id: 'Day for Night',
  perdidas: [
    {
      categoria: 'WRITING (Original Screenplay)',
      ano: 1975
    },
    {
      categoria: 'ACTRESS IN A SUPPORTING ROLE',
      ano: 1975
    },
    {
      categoria: 'DIRECTING',
      ano: 1975
    }
  ],
  vitorias: [
    {
      categoria: 'FOREIGN LANGUAGE FILM',
      ano: 1974
    }
  ]
  db.oscar.aggregate([
  real,
  { $match: { nome_do_filme: { $ne: null } } },
  { $group: {
      _id: "$nome_do_filme",
      perdidas: { $push: { $cond: [{ $in: ["$vencedor", [1, true]] }, "$$REMOVE", { categoria: "$categoria", ano: "$ano_cerimonia" }] } },
      vitorias: { $push: { $cond: [{ $in: ["$vencedor", [1, true]] }, { categoria: "$categoria", ano: "$ano_cerimonia" }, "$$REMOVE"] } }
  } },
  { $match: { $expr: { $anyElementTrue: { $map: {
      input: "$perdidas", as: "p",
      in: { $anyElementTrue: { $map: {
        input: "$vitorias", as: "w",
        in: { $and: [ { $ne: ["$$p.ano", "$$w.ano"] }, { $ne: ["$$p.categoria", "$$w.categoria"] } ] }
      } } }
  } } } } },
  { $project: { _id: 1, perdidas: 1, vitorias: 1 } }
])

  # Nível 12: Casos Práticos
Cenário 1: Curadoria de Mostra de Cinema
Você está organizando uma mostra de cinema e precisa selecionar filmes.

# 12.1 Liste os 20 filmes mais premiados do Oscar para sua mostra.
R:  _id: {
    filme: 'Titanic',
    ano: 1998
  },
  oscars: 11,
  indicacoes: 14
}
 _id: {
    filme: 'Ben-Hur',
    ano: 1960
  },
  oscars: 11,
  indicacoes: 12
}
 _id: {
    filme: 'The Lord of the Rings: The Return of the King',
    ano: 2004
  },
  oscars: 11,
  indicacoes: 11
  _id: {
    filme: 'West Side Story',
    ano: 1962
  },
  oscars: 10,
  indicacoes: 11
  _id: {
    filme: 'The English Patient',
    ano: 1997
  },
  oscars: 9,
  indicacoes: 12
   _id: {
    filme: 'The Last Emperor',
    ano: 1988
  },
  oscars: 9,
  indicacoes: 9
  _id: {
    filme: 'Gigi',
    ano: 1959
  },
  oscars: 9,
  indicacoes: 9
  _id: {
    filme: 'My Fair Lady',
    ano: 1965
  },
  oscars: 8,
  indicacoes: 12
   _id: {
    filme: 'Gone with the Wind',
    ano: 1940
  },
  oscars: 8,
  indicacoes: 12
   _id: {
    filme: 'From Here to Eternity',
    ano: 1954
  },
  oscars: 8,
  indicacoes: 12
}
 _id: {
    filme: 'Gandhi',
    ano: 1983
  },
  oscars: 8,
  indicacoes: 11
}
_id: {
    filme: 'Amadeus',
    ano: 1985
  },
  oscars: 8,
  indicacoes: 10
}
_id: {
    filme: 'Cabaret',
    ano: 1973
  },
  oscars: 8,
  indicacoes: 10
}
_id: {
    filme: 'On the Waterfront',
    ano: 1955
  },
  oscars: 8,
  indicacoes: 10
}
 _id: {
    filme: 'Slumdog Millionaire',
    ano: 2009
  },
  oscars: 8,
  indicacoes: 9
}
_id: {
    filme: 'Shakespeare in Love',
    ano: 1999
  },
  oscars: 7,
  indicacoes: 13
}
 _id: {
    filme: 'Oppenheimer',
    ano: 2024
  },
  oscars: 7,
  indicacoes: 13
   _id: {
    filme: 'Dances With Wolves',
    ano: 1991
  },
  oscars: 7,
  indicacoes: 12
   _id: {
    filme: "Schindler's List",
    ano: 1994
  },
  oscars: 7,
  indicacoes: 12
  _id: {
    filme: 'Out of Africa',
    ano: 1986
  },
  oscars: 7,
  indicacoes: 11

db.oscar.aggregate([
  ...porCategoria,
  { $group: { _id: { filme: "$_id.filme", ano: "$_id.ano" }, oscars: { $sum: "$ganhou" }, indicacoes: { $sum: 1 } } },
  { $sort: { oscars: -1, indicacoes: -1 } },
  { $limit: 22 }
])

# 12.2 Selecione 5 filmes de cada década (1930s até 2020s) que ganharam pelo menos um Oscar.
R: _id: 1930,
  top5: [
    {
      filme: 'It Happened One Night',
      ano: 1935,
      oscars: 5
    },
    {
      filme: 'The Informer',
      ano: 1936,
      oscars: 4
    },
    {
      filme: 'Anthony Adverse',
      ano: 1937,
      oscars: 4
    },
    {
      filme: 'Cimarron',
      ano: 1931,
      oscars: 3
    },
    {
      filme: 'Cavalcade',
      ano: 1933,
      oscars: 3
    }
  ]
}
 _id: 1940,
  top5: [
    {
      filme: 'Gone with the Wind',
      ano: 1940,
      oscars: 8
    },
    {
      filme: 'Going My Way',
      ano: 1945,
      oscars: 7
    },
    {
      filme: 'The Best Years of Our Lives',
      ano: 1947,
      oscars: 7
    },
    {
      filme: 'Mrs. Miniver',
      ano: 1943,
      oscars: 6
    },
    {
      filme: 'How Green Was My Valley',
      ano: 1942,
      oscars: 5
    }
  ]
}
 _id: 1950,
  top5: [
    {
      filme: 'Gigi',
      ano: 1959,
      oscars: 9
    },
    {
      filme: 'From Here to Eternity',
      ano: 1954,
      oscars: 8
    },
    {
      filme: 'On the Waterfront',
      ano: 1955,
      oscars: 8
    },
    {
      filme: 'The Bridge on the River Kwai',
      ano: 1958,
      oscars: 7
    },
    {
      filme: 'All about Eve',
      ano: 1951,
      oscars: 6

      _id: 1960,
  top5: [
    {
      filme: 'Ben-Hur',
      ano: 1960,
      oscars: 11
    },
    {
      filme: 'West Side Story',
      ano: 1962,
      oscars: 10
    },
    {
      filme: 'My Fair Lady',
      ano: 1965,
      oscars: 8
    },
    {
      filme: 'Lawrence of Arabia',
      ano: 1963,
      oscars: 7
    },
    {
      filme: 'A Man for All Seasons',
      ano: 1967,
      oscars: 6
      _id: 1970,
  top5: [
    {
      filme: 'Cabaret',
      ano: 1973,
      oscars: 8
    },
    {
      filme: 'Patton',
      ano: 1971,
      oscars: 7
    },
    {
      filme: 'The Sting',
      ano: 1974,
      oscars: 7
    },
    {
      filme: 'The Godfather Part II',
      ano: 1975,
      oscars: 6
    },
    {
      filme: 'Star Wars',
      ano: 1978,
      oscars: 6

       _id: 1980,
  top5: [
    {
      filme: 'The Last Emperor',
      ano: 1988,
      oscars: 9
    },
    {
      filme: 'Gandhi',
      ano: 1983,
      oscars: 8
    },
    {
      filme: 'Amadeus',
      ano: 1985,
      oscars: 8
    },
    {
      filme: 'Out of Africa',
      ano: 1986,
      oscars: 7
    },
    {
      filme: 'Kramer vs. Kramer',
      ano: 1980,
      oscars: 5
       _id: 1990,
  top5: [
    {
      filme: 'Titanic',
      ano: 1998,
      oscars: 11
    },
    {
      filme: 'The English Patient',
      ano: 1997,
      oscars: 9
    },
    {
      filme: 'Dances With Wolves',
      ano: 1991,
      oscars: 7
    },
    {
      filme: "Schindler's List",
      ano: 1994,
      oscars: 7
    },
    {
      filme: 'Shakespeare in Love',
      ano: 1999,
      oscars: 7
      _id: 2000,
  top5: [
    {
      filme: 'The Lord of the Rings: The Return of the King',
      ano: 2004,
      oscars: 11
    },
    {
      filme: 'Slumdog Millionaire',
      ano: 2009,
      oscars: 8
    },
    {
      filme: 'Chicago',
      ano: 2003,
      oscars: 6
    },
    {
      filme: 'American Beauty',
      ano: 2000,
      oscars: 5
    },
    {
      filme: 'Gladiator',
      ano: 2001,
      oscars: 5
      _id: 2010,
  top5: [
    {
      filme: 'Gravity',
      ano: 2014,
      oscars: 7
    },
    {
      filme: 'The Hurt Locker',
      ano: 2010,
      oscars: 6
    },
    {
      filme: 'Mad Max: Fury Road',
      ano: 2016,
      oscars: 6
    },
    {
      filme: 'La La Land',
      ano: 2017,
      oscars: 6
    },
    {
      filme: 'The Artist',
      ano: 2012,
      oscars: 5
      _id: 2020,
  top5: [
    {
      filme: 'Everything Everywhere All at Once',
      ano: 2023,
      oscars: 7
    },
    {
      filme: 'Oppenheimer',
      ano: 2024,
      oscars: 7
    },
    {
      filme: 'Dune',
      ano: 2022,
      oscars: 6
    },
    {
      filme: 'One Battle After Another',
      ano: 2026,
      oscars: 6
    },
    {
      filme: 'Anora',
      ano: 2025,
      oscars: 5

db.oscar.aggregate([
  ...porCategoria,
  { $group: { _id: { filme: "$_id.filme", ano: "$_id.ano" }, oscars: { $sum: "$ganhou" } } },
  { $match: { oscars: { $gte: 1 }, "_id.ano": { $gte: 1930 } } },
  { $sort: { oscars: -1, "_id.ano": 1 } },
  { $group: {
      _id: { $subtract: ["$_id.ano", { $mod: ["$_id.ano", 10] }] },
      filmes: { $push: { filme: "$_id.filme", ano: "$_id.ano", oscars: "$oscars" } }
  } },
  { $project: { top5: { $slice: ["$filmes", 5] } } },
  { $sort: { _id: 1 } }
])

# 12.3 Crie uma lista de "clássicos esquecidos" - filmes que ganharam Oscars, mas são de mais de 50 anos atrás.
R:_id: {
    filme: 'The Last Command',
    ano: 1928
  },
  oscars: 1,
  indicacoes: 2
}
_id: {
    filme: 'Underworld',
    ano: 1928
  },
  oscars: 1,
  indicacoes: 1
   _id: {
    filme: 'Two Arabian Knights',
    ano: 1928
  },
  oscars: 1,
  indicacoes: 1
  _id: {
    filme: 'The Dove;',
    ano: 1928
  },
  oscars: 1,
  indicacoes: 1
  _id: {
    filme: 'The Patriot',
    ano: 1929
  },
  oscars: 1,
  indicacoes: 5
  _id: {
    filme: 'The Broadway Melody',
    ano: 1929
  },
  oscars: 1,
  indicacoes: 3
  _id: {
    filme: 'Coquette',
    ano: 1929
  },
  oscars: 1,
  indicacoes: 1
  _id: {
    filme: 'The Divine Lady',
    ano: 1929
  },
  oscars: 1,
  indicacoes: 3
   _id: {
    filme: 'White Shadows in the South Seas',
    ano: 1929
  },
  oscars: 1,
  indicacoes: 1
  _id: {
    filme: 'In Old Arizona',
    ano: 1929
  },
  oscars: 1,
  indicacoes: 4
  _id: {
    filme: 'The Bridge of San Luis Rey',
    ano: 1929
  },
  oscars: 1,
  indicacoes: 1
  _id: {
    filme: 'Disraeli',
    ano: 1930
  },
  oscars: 1,
  indicacoes: 3
  _id: {
    filme: 'The Divorcee',
    ano: 1930
  },
  oscars: 1,
  indicacoes: 4
}
_id: {
    filme: 'King of Jazz',
    ano: 1930
  },
  oscars: 1,
  indicacoes: 1
   _id: {
    filme: 'With Byrd at the South Pole',
    ano: 1930
  },
  oscars: 1,
  indicacoes: 1

  _id: {
    filme: 'Skippy',
    ano: 1931
  },
  oscars: 1,
  indicacoes: 4
}
_id: {
    filme: 'A Free Soul',
    ano: 1931
  },
  oscars: 1,
  indicacoes: 3
  _id: {
    filme: 'The Dawn Patrol',
    ano: 1931
  },
  oscars: 1,
  indicacoes: 1
  _id: {
    filme: 'Min and Bill',
    ano: 1931
  },
  oscars: 1,
  indicacoes: 1
  _id: {
    filme: 'NULL',
    ano: 1931
  },
  oscars: 1,
  indicacoes: 1

const corte = new Date().getFullYear() - 50;

db.oscar.aggregate([
  ...porCategoria,
  { $group: { _id: { filme: "$_id.filme", ano: "$_id.ano" }, oscars: { $sum: "$ganhou" }, indicacoes: { $sum: 1 } } },
  { $match: { oscars: 1, "_id.ano": { $lt: corte } } },
  { $sort: { "_id.ano": 1 } },
  { $limit: 50 }
])

# Cenário 2: Análise para Documentário
Você está produzindo um documentário sobre a história do Oscar.

# 12.4 Identifique os 5 momentos mais importantes (cerimônias com mais premiações históricas).
R:_id: {
    filme: 'Titanic',
    ano: 1998
  },
  oscars: 11
}
 _id: {
    filme: 'Ben-Hur',
    ano: 1960
  },
  oscars: 11
  _id: {
    filme: 'The Lord of the Rings: The Return of the King',
    ano: 2004
  },
  oscars: 11
}
 _id: {
    filme: 'West Side Story',
    ano: 1962
  },
  oscars: 10
   _id: {
    filme: 'The Last Emperor',
    ano: 1988
  },
  oscars: 9
db.oscar.aggregate([
  ...porCategoria,
  { $group: { _id: { filme: "$_id.filme", ano: "$_id.ano" }, oscars: { $sum: "$ganhou" } } },
  { $sort: { oscars: -1 } },
  { $limit: 8 }
])

# 12.5 Liste todos os "primeiros" históricos (primeira mulher a ganhar melhor direção, primeiro ator negro, etc.) - use sua criatividade para encontrar esses marcos.
R:     ano_cerimonia: 1940,
    Hattie McDaniel (1940): primeira pessoa negra indicada e vencedora do Oscar, como atriz coadjuvante em Gone with the Wind.
Sidney Poitier (1964): primeiro homem negro a vencer Melhor Ator.
Halle Berry (2002): primeira mulher negra a vencer Melhor Atriz.
Lina Wertmüller (1977): primeira mulher indicada a Melhor Direção.
Kathryn Bigelow (2010): primeira mulher a vencer Melhor Direção.
Beauty and the Beast (1992): primeira animação indicada a Melhor Filme.
Parasite (2020): primeiro filme em língua não inglesa a vencer Melhor Filme.
Fernanda Montenegro (1999), Fernanda Torres (2025) e Wagner Moura (2026): brasileiros indicados em categorias de atuação

const marcos = [
  ["Hattie McDaniel", /^ACTRESS/],
  ["Sidney Poitier", /^ACTOR/],
  ["Halle Berry", /^ACTRESS/],
  ["Lina Wertmüller", /DIRECTING/],
  ["Kathryn Bigelow", /DIRECTING/],
  ["Chloé Zhao", /DIRECTING/],
  ["Fernanda Montenegro", /^ACTRESS/],
  ["Fernanda Torres", /^ACTRESS/],
  ["Wagner Moura", /^ACTOR/]
];
marcos.forEach(([nome, cat]) => {
  printjson(db.oscar.find(
    { nome_do_indicado: nome, categoria: cat },
    { _id: 0, nome_do_indicado: 1, ano_cerimonia: 1, categoria: 1, nome_do_filme: 1, vencedor: 1 }
  ).sort({ ano_cerimonia: 1 }).toArray());
});

# 12.6 Encontre casos de "injustiça" - filmes/atores muito indicados, mas que nunca ganharam.
R: _id: {
    filme: 'True Grit',
    ano: 2011
  },
  indicacoes: 10,
  oscars: 0
}
  _id: {
    filme: 'Killers of the Flower Moon',
    ano: 2024
  },
  indicacoes: 10,
  oscars: 0
}
  _id: {
    filme: 'Gangs of New York',
    ano: 2003
  },
  indicacoes: 10,
  oscars: 
    _id: {
    filme: 'American Hustle',
    ano: 2014
  },
  indicacoes: 10,
  oscars: 0
    _id: {
    filme: 'The Turning Point',
    ano: 1978
  },
  indicacoes: 10,
  oscars: 0
    _id: {
    filme: 'The Color Purple',
    ano: 1986
  },
  indicacoes: 10,
  oscars: 0
}
 _id: {
    filme: 'The Irishman',
    ano: 2020
  },
  indicacoes: 9,
  oscars: 0
    _id: {
    filme: 'Marty Supreme',
    ano: 2026
  },
  indicacoes: 9,
  oscars: 0
    _id: {
    filme: 'The Elephant Man',
    ano: 1981
  },
  indicacoes: 8,
  oscars: 0

  id: {
    filme: "The Nun's Story",
    ano: 1960
  },
  indicacoes: 8,
  oscars: 0
   _id: {
    filme: 'Elvis',
    ano: 2023
  },
  indicacoes: 8,
  oscars: 0
    _id: {
    filme: 'The Banshees of Inisherin',
    ano: 2023
  },
  indicacoes: 8,
  oscars: 0
   _id: {
    filme: 'The Remains of the Day',
    ano: 1994
  },
  indicacoes: 8,
  oscars: 0
   _id: {
    filme: 'The Sand Pebbles',
    ano: 1967
  },
  indicacoes: 8,
  oscars: 0
   _id: {
    filme: 'Ragtime',
    ano: 1982
  },
  indicacoes: 8,
  oscars: 0
}
  _id: {
    filme: 'The Little Foxes',
    ano: 1942
  },
  indicacoes: 8,
  oscars: 0
    _id: {
    filme: 'A Complete Unknown',
    ano: 2025
  },
  indicacoes: 8,
  oscars: 0
    _id: {
    filme: 'Star!',
    ano: 1969
  },
  indicacoes: 7,
  oscars: 0
   _id: {
    filme: 'Quo Vadis',
    ano: 1952
  },
  indicacoes: 7,
  oscars: 0
   _id: {
    filme: 'The Martian',
    ano: 2016
  },
  indicacoes: 7,
  oscars: 0
  
db.oscar.aggregate([
  ...porCategoria,
  { $group: { _id: { filme: "$_id.filme", ano: "$_id.ano" }, indicacoes: { $sum: 1 }, oscars: { $sum: "$ganhou" } } },
  { $match: { oscars: 0, indicacoes: { $gte: 7 } } },
  { $sort: { indicacoes: -1 } }
])

# Cenário 3: Estatísticas para Apostas
Você trabalha para um site de apostas e precisa de estatísticas.

# 12.7 Qual a probabilidade histórica de um filme indicado em 10 categorias ganhar Melhor Filme?
R: filmes: 92,
  venceram: 36,
  probabilidade_percentual: 39.130434782608695

db.oscar.aggregate([
  ...porCategoria,
  { $group: {
      _id: { filme: "$_id.filme", ano: "$_id.ano" },
      indicacoes: { $sum: 1 },
      melhor_filme: { $max: { $cond: [{ $in: ["$_id.categoria", pic] }, "$ganhou", 0] } }
  } },
  { $match: { indicacoes: { $gte: 10 } } },
  { $group: { _id: null, filmes: { $sum: 1 }, venceram: { $sum: "$melhor_filme" } } },
  { $project: {
      _id: 0, filmes: 1, venceram: 1,
      probabilidade_percentual: { $multiply: [{ $divide: ["$venceram", "$filmes"] }, 100] }
  } }
])

# 12.8 Atores que ganharam Melhor Ator tendem a ter quantas indicações antes da primeira vitória?
R:  atores: 88,
  media_indicacoes_antes: 0.8409090909090909
  db.oscar.aggregate([
  { $match: { categoria: { $in: ["ACTOR", "ACTOR IN A LEADING ROLE"] } } },
  { $group: {
      _id: "$nome_do_indicado",
      anos: { $push: "$ano_cerimonia" },
      primeira_vitoria: { $min: { $cond: [{ $in: ["$vencedor", [1, true]] }, "$ano_cerimonia", null] } }
  } },
  { $match: { primeira_vitoria: { $ne: null } } },
  { $addFields: { antes: { $size: { $filter: { input: "$anos", as: "a", cond: { $lt: ["$$a", "$primeira_vitoria"] } } } } } },
  { $group: { _id: null, atores: { $sum: 1 }, media_indicacoes_antes: { $avg: "$antes" } } }
])

# 12.9 Qual categoria tem os vencedores mais "previsíveis" (mesmo artista/filme ganha múltiplas vezes)?
R: _id: 'SHORT SUBJECT (Cartoon)',
  campeao: 'Walt Disney, Producer',
  maior_repeticao: 12,
  total_vitorias: 39,
  vencedores_diferentes: 18,
  concentracao: 0.3076923076923077

db.oscar.aggregate([
  real,
  { $match: { vencedor: venceu, nome_do_indicado: { $ne: null } } },
  { $group: { _id: { categoria: "$categoria", nome: "$nome_do_indicado" }, vitorias: { $sum: 1 } } },
  { $sort: { vitorias: -1 } },
  { $group: {
      _id: "$_id.categoria",
      campeao: { $first: "$_id.nome" },
      maior_repeticao: { $max: "$vitorias" },
      total_vitorias: { $sum: "$vitorias" },
      vencedores_diferentes: { $sum: 1 }
  } },
  { $match: { total_vitorias: { $gte: 10 } } },
  { $addFields: { concentracao: { $divide: ["$maior_repeticao", "$total_vitorias"] } } },
  { $sort: { maior_repeticao: -1 } },
  { $limit: 10 }
])
# Nível 13: Queries Criativas
# 13.1 Encontre todos os filmes cujo nome começa com "The" e ganharam pelo menos um Oscar.
R:

  'The Abyss',
  'The Accidental Tourist',
  'The Accused',
  'The Adventures of Don Juan',
  'The Adventures of Priscilla, Queen of the Desert',
  'The Adventures of Robin Hood',
  'The African Queen',
  'The Age of Innocence',
  'The Alamo',
  'The Alaskan Eskimo',
  'The Anderson Platoon',
  'The Apartment',
  'The Appointments of Dennis Jennings',
  'The Artist',
  'The Assault',
  'The Aviator',
  'The Awful Truth',
  'The Bachelor and the Bobby-Soxer',
  'The Bad and the Beautiful',
  'The Barbarian Invasions',
  'The Barefoot Contessa',
  'The Battle of Midway',
  "The Bells of St. Mary's",
  'The Bespoke Overcoat',
  'The Best Years of Our Lives',
  'The Big Broadcast of 1938',
  'The Big Country',
  'The Big House',
  'The Big Short',
  "The Bishop's Wife",
  'The Black Stallion',
  'The Black Swan',
  'The Blind Side',
  'The Blood of Yingzhou District',
  'The Bolero',
  'The Bourne Ultimatum',
  'The Box',
  'The Boy and the Heron',
  'The Boy, the Mole, the Fox and the Horse',
  'The Brave One',
  'The Bridge of San Luis Rey',
  'The Bridge on the River Kwai',
  'The Bridges at Toko-Ri',
  'The Broadway Melody',
  'The Brutalist',
  'The Buddy Holly Story',
  'The Candidate',
  'The Cat Concerto',
  'The Champ',
  'The Charge of the Light Brigade',
  'The Chicken (Le Poulet)',
  'The Chronicles of Narnia: The Lion, the Witch and the Wardrobe',
  'The ChubbChubbs!',
  'The Cider House Rules',
  'The Color of Money',
  'The Constant Gardener',
  'The Counterfeiters',
  'The Country Cousin',
  'The Country Girl',
  'The Cove',
  'The Cowboy and the Lady',
  'The Critic',
  'The Crunch Bird',
  'The Crying Game',
  'The Curious Case of Benjamin Button',
  'The Danish Girl',
  'The Danish Poet',
  'The Dark Angel',
  'The Dark Knight',
  'The Dawn Patrol',
  'The Deer Hunter',
  'The Defiant Ones',
  'The Departed',
  'The Descendants',
  'The Diary of Anne Frank',
  'The Dirty Dozen',
  'The Discreet Charm of the Bourgeoisie',
  'The Divine Lady',
  'The Divorcee',
  'The Dollar Bottom',
  'The Dot and the Line',
  'The Dove;',
  'The Duchess',
  'The Eleanor Roosevelt Story',
  'The Elephant Whisperers',
  'The Empire Strikes Back',
  'The End of the Game',
  'The Enemy Below',
  'The English Patient',
  'The Exorcist',
  'The Eyes of Tammy Faye',
  'The Face of Lincoln',
  'The Facts of Life',
  'The Fantastic Flying Books of Mr. Morris Lessmore',
  "The Farmer's Daughter",
  'The Father',
  'The Favourite',
  'The Fighter',
  'The Fighting Lady',
  'The Fisher King',
  'The Flight of the Gossamer Condor',
  'The Fly',
  'The Fog of War',
  'The Fortune Cookie',
  'The French Connection',
  'The Fugitive',
  'The Full Monty',
  'The Garden of the Finzi Continis',
  'The Gay Divorcee',
  'The Ghost and the Darkness',
  'The Girl Who Cried Pearls',
  'The Girl with the Dragon Tattoo',
  'The Glenn Miller Story',
  'The Godfather',
  'The Godfather Part II',
  'The Golden Compass',
  'The Golden Fish',
  'The Good Earth',
  'The Goodbye Girl',
  'The Graduate',
  'The Grand Budapest Hotel',
  'The Grapes of Wrath',
  'The Great American Cowboy',
  'The Great Beauty',
  'The Great Caruso',
  'The Great Gatsby',
  'The Great Lie',
  'The Great McGinty',
  'The Great Race',
  'The Great Waltz',
  'The Great Ziegfeld',
  'The Greatest Show on Earth',
  'The Guns of Navarone',
  'The Harvey Girls',
  'The Hateful Eight',
  'The Heiress',
  'The Hellstrom Chronicle',
  'The Help',
  'The High and the Mighty',
  'The Hindenburg',
  'The Holdovers',
  'The Hole',
  'The Horse with the Flying Tail',
  'The Hospital',
  'The Hours',
  'The House on 92nd Street',
  'The Human Comedy',
  'The Hunt for Red October',
  'The Hurricane',
  'The Hurt Locker',
  'The Hustler',
  'The Imitation Game',
  'The Incredibles',
  'The Informer',
  'The Invaders',
  'The Iron Lady',
  'The Johnstown Flood',
  'The Joker Is Wild',
  'The Jolson Story',
  'The Jungle Book',
  'The Killing Fields',
  'The King and I',
  "The King's Speech",
  'The Lady in Number 6: Music Saved My Life',
  'The Last Command',
  'The Last Days',
  'The Last Emperor',
  'The Last King of Scotland',
  'The Last Picture Show',
  'The Last Repair Shop',
  'The Last of the Mohicans',
  'The Lavender Hill Mob',
  'The Life of Emile Zola',
  'The Lion King',
  'The Lion in Winter',
  'The Little Mermaid',
  'The Little Orphan',
  'The Lives of Others',
  'The Lives of a Bengal Lancer',
  'The Living Desert',
  'The Long Goodbye',
  'The Long Way Home',
  'The Longest Day',
  'The Lord of the Rings: The Fellowship of the Ring',
  'The Lord of the Rings: The Return of the King',
  'The Lord of the Rings: The Two Towers',
  'The Lost Thing',
  'The Lost Weekend',
  'The Lunch Date',
  'The Madness of King George',
  'The Magic Machines',
  'The Man Who Knew Too Much',
  'The Man Who Planted Trees',
  'The Man Who Skied down Everest',
  'The Matrix',
  'The Merry Widow',
  'The Merry Wives of Windsor Overture',
  'The Milagro Beanfield War',
  'The Milky Way',
  'The Miracle Worker',
  'The Mission',
  'The Moon and the Son: An Imagined Conversation',
  'The More the Merrier',
  'The Motorcycle Diaries',
  'The Muppets',
  'The Music Box',
  'The Naked City',
  "The Neighbors' Window",
  'The New Tenants',
  'The Night of the Iguana',
  'The Nights of Cabiria',
  'The Nutty Professor',
  'The Official Story',
  'The Old Man and the Sea',
  'The Old Mill',
  'The Omen',
  'The Only Girl in the Orchestra',
  'The Paleface',
  'The Panama Deception',
  'The Paper Chase',
  'The Patriot',
  'The Personals: Improvisations on Romance in the Golden Years',
  'The Philadelphia Story',
  'The Phone Call',
  'The Pianist',
  'The Piano',
  'The Picture of Dorian Gray',
  'The Pink Phink',
  'The Poseidon Adventure',
  'The Postman (Il Postino)',
  'The Power of the Dog',
  'The Pride of the Yankees',
  'The Prime of Miss Jean Brodie',
  'The Prince of Egypt',
  'The Private Life of Henry VIII',
  'The Private Life of the Gannets',
  'The Producers',
  'The Public Pays',
  'The Queen',
  'The Queen of Basketball',
  'The Quiet Man',
  'The Rains Came',
  "The Razor's Edge",
  'The Reader',
  'The Red Balloon',
  'The Red Shoes',
  'The Red Violin',
  'The Redwoods',
  'The Resurrection of Broncho Billy',
  'The Revenant',
  'The Right Stuff',
  'The River',
  'The Robe',
  'The Rose Tattoo',
  'The Salesman',
  'The Sand Castle',
  'The Sandpiper',
  'The Scoundrel',
  'The Sea Inside',
  'The Sea around Us',
  'The Search',
  'The Secret Land',
  'The Secret in Their Eyes',
  'The Seventh Veil',
  'The Shape of Water',
  'The Shop on Main Street',
  'The Shore',
  'The Silence of the Lambs',
  'The Silent Child',
  'The Silent World',
  'The Sin of Madelon Claudet',
  'The Singers',
  'The Snake Pit',
  'The Social Network',
  'The Solid Gold Cadillac',
  'The Song of Bernadette',
  'The Sound of Music',
  'The Sting',
  'The Stone Carvers',
  'The Story of Louis Pasteur',
  'The Stratton Story',
  'The Subject Was Roses',
  'The Substance',
  'The Sunshine Boys',
  'The Ten Commandments',
  'The Ten-Year Lunch: The Wit and Legend of the Algonquin Round Table',
  'The Theory of Everything',
  'The Thief of Bagdad',
  'The Third Man',
  'The Thomas Crown Affair',
  'The Three Faces of Eve',
  'The Three Little Pigs',
  'The Time Machine',
  'The Times of Harvey Milk',
  'The Tin Drum',
  'The Titan: Story of Michelangelo',
  'The Tortoise and the Hare',
  'The Towering Inferno',
  'The Treasure of the Sierra Madre',
  'The Trip to Bountiful',
  'The True Glory',
  'The True Story of the Civil War',
  'The Two Mouseketeers',
  'The Ugly Duckling',
  'The Untouchables',
  'The Usual Suspects',
  'The V.I.P.s',
  'The Vanishing Prairie',
  'The Virgin Spring',
  'The War Game',
  'The War of the Worlds',
  'The Way We Were',
  'The Westerner',
  'The Wetback Hound',
  'The Whale',
  'The White Helmets',
  'The Windshield Wiper',
  'The Wizard of Oz',
  'The Wolfman',
  'The Woman in Red',
  'The Wonderful Story of Henry Sugar',
  'The Wonderful World of the Brothers Grimm',
  'The Wrong Trousers',
  'The Year of Living Dangerously',
  'The Yearling',
  'The Young Victoria',
  'The Zone of Interest'

  db.oscar.distinct("nome_do_filme", { nome_do_filme: /^The /, vencedor: venceu })

# 13.2 Liste todos os indicados cujo nome contém um sobrenome composto (ex: "Mary-Louise Parker").
R:  '20th Century-Fox',
  '20th Century-Fox Film Corporation in recognition of their imagination',
  '20th Century-Fox Studio',
  '20th Century-Fox Studio Music Department, Louis Silvers, head of department  (no composer credit)',
  '20th Century-Fox Studio Sound Department',
  '20th Century-Fox Studio Sound Department, Carl Faulkner, Sound Director',
  '20th Century-Fox Studio Sound Department, Carl W. Faulkner, Sound Director',
  '20th Century-Fox Studio Sound Department, E. H. Hansen, Sound Director',
  '20th Century-Fox Studio Sound Department, Edmund H. Hansen, Sound Director',
  '20th Century-Fox Studio Sound Department, James P. Corcoran, Sound Director',
  '20th Century-Fox Studio Sound Department, James P. Corcoran, Sound Director; and Todd-AO Sound Department, Fred Hynes, Sound Director',
  '20th Century-Fox Studio Sound Department, Thomas T. Moulton, Sound Director',
  'Ai-Ling Lee and Mildred Iatrou Morgan',
  'Alain Gagnol and Jean-Loup Felicioli',
  'Alain Robbe-Grillet',
  'Alison Nigh-Strelich, Producer',
  'Ana López-Puigcerver, David Martí and Montse Ribé',
  'Anders Østergaard and Lise Lense-Møller',
  'Andrew Coats and Lou Hamou-Lhadj',
  'Andy Nelson, Ai-Ling Lee and Steve A. Morrow',
  'Ann-Margret',
  'Anna Biedrzycka-Sheppard',
  'Anne Rosellini and Alix Madigan-Yorkin, Producers',
  'Anne-Marie Marchand',
  'Anne-Sophie Bion and Michel Hazanavicius',
  'Anthony Havelock-Allan and John Brabourne, Producers',
  'Argosy-Wanger',
  'Armin Mueller-Stahl',
  'Art Direction:  Aline Bonetto; Set Decoration:  Marie-Laure Valla',
  'Art Direction:  Gianni Quaranta, Brian Ackland-Snow;  Set Decoration:  Brian Savegar, Elio Altamura',
  'Art Direction:  Henry Bumstead;  Set Decoration:  Janice Blackie-Goodine',
  'Art Direction:  Jean Rabasse;  Set Decoration:  Françoise Benoît-Fresco',
  'Art Direction:  Mark-Lee Kirk;  Interior Decoration:  Victor A. Gangelin',
  'Art Direction:  Max Parker, Mark-Lee Kirk;  Interior Decoration:  Casey Roberts',
  'Art Direction:  Urie McCleary, Gil Parrondo;  Set Decoration:  Antonio Mateos, Pierre-Louis Thevenet',
  'Arthur Freed for distinguished service to the Academy and the production of six top-rated Awards telecasts.',
  'Asif Kapadia and James Gay-Rees',
  'Bill Westenhofer, Guillaume Rocheron, Erik-Jan De Boer and Donald R. Elliott',
  'Bruno Collet and Jean-François Le Corre',
  'Bunny Dull (Metro-Goldwyn-Mayer)',
  'Catherine Zeta-Jones',
  'Cedric Nicolas-Troyan, Philip Brennan, Neil Corbould and Michael Dawson',
  'Charles Dorian (Metro-Goldwyn-Mayer)',
  'Charley Henley, Luc-Ewen Martin-Fenouillet, Simone Coco and Neil Corbould',
  'Ciné-Documents',
  'Claude Berri, Producer;  Timothy Burrill, Co-Producer',
  'Costa-Gavras',
  'Costa-Gavras, Donald Stewart',
  'Daniel Day-Lewis',
  'Daniel Junge and Sharmeen Obaid-Chinoy',
  'David Lean, Anthony Havelock-Allan, Ronald Neame',
  'David Parfitt, Jean-Louis Livi and Philippe Carcassonne, Producers',
  'Dee Hibbert-Jones and Nomi Talisman',
  'Denmark; O. Dalsgaard-Olsen, Producer',
  'Dido-Film-GmbH',
  'Domee Shi and Becky Neiman-Cobb',
  'Douglas Fairbanks (Commemorative Award) - recognizing the unique and outstanding contribution of Douglas Fairbanks',
  'Dr. Frank Daniel and Izak Ben-Meir, Producers',
  'Elizabeth Yianni-Georgiou and David White',
  'Federal Republic of Germany - West; Gyula Trebitsch and Walter Koppel, Producers',
  'Forbidden Games - Best Foreign Language Film first released in the United States during 1952.',
  'Francis Coppola, Producer;  Fred Roos, Gray Frederickson and Tom Sternberg, Co-Producers',
  'Francis Ford Coppola, Producer;  Fred Roos, Co-Producer',
  'Francis Ford Coppola, Producer;  Gary Kurtz, Co-Producer',
  'Francis Ford Coppola, Producer;  Gray Frederickson and Fred Roos, Co-Producers',
  'Francois Truffaut, Jean-Louis Richard, Suzanne Schiffman',
  'Gary Gilbert, Jeffrey Levy-Hinte and Celine Rattray, Producers',
  'Gate of Hell - Best Foreign Language Film first released in the United States during 1954.',
  'Gian-Carlo Menotti',
  'Harman-Ising',
  'Henry-Alex Rubin and Dana Adam Shapiro',
  'Howard Hawks - A master American filmmaker whose creative efforts hold a distinguished place in world cinema.',
  'J. Arthur Rank-Archers',
  'J. Arthur Rank-Cineguild',
  'J. Arthur Rank-Two Cities Films',
  'Jacques-Yves Cousteau, Producer',
  'James Baskett for his able and heart-warming characterization of Uncle Remus',
  'Jean Hersholt - in recognition of his service to the Academy during four terms as president.',
  'Jean Renoir - a genius who',
  'Jean-Charles Meunier, Producer',
  'Jean-Claude Carrière',
  'Jean-Claude Carrière, Philip Kaufman',
  'Jean-Luc Godard for passion. For confrontation. For a new kind of cinema.',
  'Jean-Paul Rappeneau, Ariane Mnouchkine, Daniel Boulanger, Philippe De Broca',
  'Jean-Pierre Dorleac',
  'Jean-Pierre Dutilleux, Barry Williams and Michel Gast, Producers',
  'Jean-Xavier de Lestrade, Denis Poncet',
  'Jessica Kingdon, Kira Simon-Kennedy and Nathan Truesdell',
  'Joel Harlow and Gloria Pasqua-Casny',
  'John S. Waters (Metro-Goldwyn-Mayer)',
  'Jon Taylor, Frank A. Montaño, Ai-Ling Lee and Mary H. Ellis',
  'Jorge Semprun, Costa-Gavras',
  'Karen Hartley Thomas, Suzi Battersby and Ashra Kelly-Blue',
  'Kazu Hiro, Kay Georgiou and Lori McCoy-Bell',
  'Keisha Castle-Hughes',
  'Kodi Smit-McPhee',
  'Lee Daniels, Sarah Siegel-Magness and Gary Magness, Producers',
  'Luis Buñuel, Jean-Claude Carrière',
  'Marc-Andreas Bochert, Gabriele Lins',
  'Marianne Farley and Marie-Hélène Panisset',
  'Marianne Jean-Baptiste',
  'Marie-Ange Luciani and David Thion, Producers',
  'Marie-Christine Barrault',
  'Marilyn Vance-Straker',
  'Martin Strange-Hansen and Kim Magnusson',
  'Martin Strange-Hansen, Mie Andreasen',
  'Metro-Goldwyn-Mayer',
  'Metro-Goldwyn-Mayer British Studio Sound Department, A. W. Watkins, Sound Director; and Metro-Goldwyn-Mayer Studio Sound Department, Franklin E. Milton, Sound Director',
  'Metro-Goldwyn-Mayer London Studio Sound Department, A. W. Watkins, Sound Director',
  'Metro-Goldwyn-Mayer Studio Music Department, Nat W. Finston, head of department  (Score by Herbert Stothart)',
  'Metro-Goldwyn-Mayer Studio Sound Department',
  'Metro-Goldwyn-Mayer Studio Sound Department, A. W. Watkins, Sound Director',
  'Metro-Goldwyn-Mayer Studio Sound Department, Douglas Shearer, Sound Director',
  'Metro-Goldwyn-Mayer Studio Sound Department, Dr. Wesley C. Miller, Sound Director',
  'Metro-Goldwyn-Mayer Studio Sound Department, Franklin E. Milton, Sound Director',
  'Metro-Goldwyn-Mayer Studio Sound Department, Wesley C. Miller, Sound Director',
  'Metro-Goldwyn-Mayer for its achievement in representing the American Way of Life in the production of the Andy Hardy" series of films."',
  'Michèle Burke, Jean-Pierre Eychenne',
  'Mike Le-Mare',
  'Milan Bor, Trevor Pyke, Mike Le-Mare',
  'Monsieur Vincent - voted by the Academy Board of Governors as the most outstanding foreign language film released in the United States during 1948.',
  'Mstyslav Chernov, Michelle Mizner and Raney Aronson-Rath',
  'Music and Lyric by DIXSON and Beyoncé Knowles-Carter',
  'Music and Lyric by Kristen Anderson-Lopez and Robert Lopez',
  'Music and Lyric by Lin-Manuel Miranda',
  'Music by Claude-Michel Schönberg; Lyric by Herbert Kretzmer and Alain Boublil',
  'Music by Jack Nitzsche and Buffy Sainte-Marie; Lyric by Will Jennings',
  'Oliver Marsh and Allen Davey for the color cinematography of the Metro-Goldwyn-Mayer production',
  'Orry-Kelly',
  'Orry-Kelly, Walter Plunkett, Irene Sharaff',
  'Pablo Helman, Leandro Estebecorena, Nelson Sepulveda-Fauser and Stephane Grabli',
  'Peter Capaldi, Ruth Kenley-Letts',
  'Philippe Pollet-Villard',
  'Production Design: David Wasco; Set Decoration: Sandy Reynolds-Wasco',
  'Production Design: Eve Stewart; Set Decoration: Anna Lynch-Robinson',
  'Rashomon - voted by the Board of Governors as the most outstanding foreign language film released in the United States during 1951.',
  'Richard Francis-Bruce',
  'Rob Bottin, Peter Robb-King',
  'Robert Knudson, Robert J. Glass, Richard Tyler, Jean-Louis Ducarme',
  'Sam Mendes, Pippa Harris, Jayne-Ann Tenggren and Callum McDougall, Producers',
  'Samuel Goldwyn - United Artists Studio Sound Department',
  'Samuel Goldwyn Studio Sound Department, Gordon E. Sawyer, Sound Director; and Todd-AO Sound Department, Fred Hynes, Sound Director',
  'Screenplay - Edward Berger, Lesley Paterson & Ian Stokell',
  'Screenplay - Justine Triet and Arthur Harari',
  'Screenplay by Guillaume Laurant and Jean-Pierre Jeunet; Dialogue by Guillaume Laurant',
  'Sergio Lopez-Rivera, Mia Neal and Jamika Wilson',
  'Sharmeen Obaid-Chinoy',
  'Shoe-Shine - the high quality of this motion picture',
  'Shui-Bo Wang, Donald McWilliams',
  'Simon Channing-Williams, Producer',
  'St. Francis-Xavier University, Antigonish, Nova Scotia',
  'Story and Screenplay by Jean-Charles Tacchella;  Adaptation by Daniele Thompson',
  'Story and Screenplay by Luis Buñuel;  in collaboration with Jean-Claude Carrière',
  'The Bicycle Thief - voted by the Academy Board of Governors as the most outstanding foreign language film released in the United States during 1949.',
  'The March of Time for its significance to motion pictures and for having revolutionized one of the most important branches of the industry - the newsreel.',
  'The Motion Picture Relief Fund - acknowledging the outstanding services to the industry during the past year of the Motion Picture Relief Fund and its progressive leadership.  Presented to Jean Hersholt',
  'The Walls of Malapaga - voted by the Board of Governors as the most outstanding foreign language film released in the United States in 1950.',
  'To Ryan Laney for his innovative adaptation and deployment of AI-driven facial veiling technology used to protect the identities while preserving the visual relatability of subjects in documentary filmmaking as exemplified in Welcome to Chechnya (2020).',
  'Todd-AO Sound Department, Fred Hynes, Sound Director',
  'Todd-AO Sound Department, Fred Hynes, Sound Director; and Samuel Goldwyn Studio Sound Department, Gordon E. Sawyer, Sound Director',
  'Tom Burman, Bari Dreiband-Burman',
  'Tony Pierce-Roberts',
  'Ulla-Britt Soderlund, Milena Canonero',
  'Van Nest Polglase, Mark-Lee Kirk',
  'Vincent René-Lortie and Samuel Caron',
  'Vincenzo Lucci-Chiarissi, Producer',
  'Vivienne Verdon-Roe and Eric Thiermann, Producers',
  'Vivienne Verdon-Roe, Producer',
  'Waad al-Kateab and Edward Watts',
  'William Tuttle for his outstanding make-up achievement for 7 Faces of Dr. Lao.',
  'Wim Wenders and Gian-Piero Ringel',
  'Written by Sam Mendes & Krysty Wilson-Cairns',
  'Yi Seung-Jun and Gary Byung-Seok Kam',
  'Yuh-Jung Youn',
  'the Technicolor Company for its contributions in successfully bringing three-color feature production to the screen.'
db.oscar.distinct("nome_do_indicado", { nome_do_indicado: /-/ })

# 13.3 Encontre todas as cerimônias onde houve empate (múltiplos vencedores na mesma categoria no mesmo ano).
R:  _id: {
    ano: 1932,
    categoria: 'ACTOR'
  },
  filmes: [
    'Dr. Jekyll and Mr. Hyde',
    'The Champ'
  ],
  indicados: [
    'Fredric March',
    'Wallace Beery'
     _id: {
    ano: 1943,
    categoria: 'DOCUMENTARY'
  },
  filmes: [
    'Prelude to War',
    'Moscow Strikes Back',
    'The Battle of Midway',
    'Kokoda Front Line!'
  ],
  indicados: [
    'Artkino',
    'United States Navy',
    'United States Army Special Services',
    'Australian News & Information Bureau'
    _id: {
    ano: 1950,
    categoria: 'DOCUMENTARY (Short Subject)'
  },
  filmes: [
    'So Much for So Little',
    'A Chance to Live'
  ],
  indicados: [
    'Edward Selzer, Producer',
    'Richard de Rochemont, Producer'
    _id: {
    ano: 1969,
    categoria: 'ACTRESS'
  },
  filmes: [
    'The Lion in Winter',
    'Funny Girl'
  ],
  indicados: [
    'Katharine Hepburn',
    'Barbra Streisand'
    _id: {
    ano: 1977,
    categoria: 'SPECIAL ACHIEVEMENT AWARD (Visual Effects)'
  },
  filmes: [
    'King Kong',
    "Logan's Run"
  ],
  indicados: [
    'L. B. Abbott, Glen Robinson, Matthew Yuricich',
    'Carlo Rambaldi, Glen Robinson, Frank Van der Veer'
  ]
  _id: {
    ano: 1987,
    categoria: 'DOCUMENTARY (Feature)'
  },
  filmes: [
    "Artie Shaw: Time Is All You've Got",
    'Down and Out in America'
  ],
  indicados: [
    'Brigitte Berman, Producer',
    'Joseph Feury and Milton Justice, Producers'
  ]
  _id: {
    ano: 1995,
    categoria: 'SHORT FILM (Live Action)'
  },
  filmes: [
    'Trevor',
    "Franz Kafka's It's a Wonderful Life"
  ],
  indicados: [
    'Peter Capaldi, Ruth Kenley-Letts',
    'Peggy Rajski, Randy Stone'
    _id: {
    ano: 2013,
    categoria: 'SOUND EDITING'
  },
  filmes: [
    'Skyfall',
    'Zero Dark Thirty'
  ],
  indicados: [
    'Per Hallberg and Karen Baker Landers',
    'Paul N.J. Ottosson'
    _id: {
    ano: 2026,
    categoria: 'SHORT FILM (Live Action)'
  },
  filmes: [
    'Two People Exchanging Saliva',
    'The Singers'
  ],
  indicados: [
    'Two People Exchanging Saliva',
    'The Singers'
    
db.oscar.aggregate([
  { $match: { vencedor: venceu, ficticio: { $ne: true } } },
  { $group: {
      _id: { ano: "$ano_cerimonia", categoria: "$categoria" },
      filmes: { $addToSet: "$nome_do_filme" },
      indicados: { $addToSet: "$nome_do_indicado" }
  } },
  { $match: { $expr: { $and: [
      { $gt: [{ $size: "$filmes" }, 1] },
      { $gt: [{ $size: "$indicados" }, 1] }
  ] } } },
  { $sort: { "_id.ano": 1 } }
])

# 13.4 Crie uma query que simule uma "loteria" - selecione 5 filmes aleatórios que ganharam Melhor Filme.
R: _id: {
    filme: 'The Godfather Part II',
    ano: 1975
      _id: {
    filme: 'The Sound of Music',
    ano: 1966
  }
    _id: {
    filme: 'Nomadland',
    ano: 2021
  }
    _id: {
    filme: 'Rebecca',
    ano: 1941
     _id: {
    filme: 'Titanic',
    ano: 1998

    db.oscar.aggregate([
  { $match: { categoria: { $in: pic }, vencedor: venceu, ficticio: { $ne: true } } },
  { $group: { _id: { filme: "$nome_do_filme", ano: "$ano_cerimonia" } } },
  { $sample: { size: 5 } }
])

# 13.5 Encontre padrões nos nomes dos filmes vencedores (ex: quantos têm uma palavra só, duas palavras, etc.).
R: QUANTIDADE DE PALABRAS:
  _id: 1,
  filmes: 353
   _id: 2,
  filmes: 347
  _id: 3,
  filmes: 364
   _id: 4,
  filmes: 183
  _id: 5,
  filmes: 113
   _id: 6,
  filmes: 55
  _id: 7,
  filmes: 13

db.oscar.aggregate([
  { $match: { vencedor: venceu, nome_do_filme: { $type: "string" }, ficticio: { $ne: true } } },
  { $group: { _id: { filme: "$nome_do_filme", ano: "$ano_cerimonia" } } },
  { $addFields: { palavras: { $size: { $split: [{ $trim: { input: "$_id.filme" } }, " "] } } } },
  { $group: { _id: "$palavras", filmes: { $sum: 1 } } },
  { $sort: { _id: 1 } }
])


PALAVRA DE ABERTURA MAIS COMUM

  _id: 'The',
  filmes: 330
    _id: 'A',
  filmes: 42
    _id: 'Black',
  filmes: 9
    _id: 'All',
  filmes: 9
   _id: 'In',
  filmes: 8
    _id: 'My',
  filmes: 7
    _id: 'One',
  filmes: 7
    _id: 'La',
  filmes: 6
    _id: 'King',
  filmes: 6

db.oscar.aggregate([
  { $match: { vencedor: venceu, nome_do_filme: { $type: "string" }, ficticio: { $ne: true } } },
  { $group: { _id: { filme: "$nome_do_filme", ano: "$ano_cerimonia" } } },
  { $addFields: { primeira: { $arrayElemAt: [{ $split: ["$_id.filme", " "] }, 0] } } },
  { $group: { _id: "$primeira", filmes: { $sum: 1 } } },
  { $sort: { filmes: -1 } },
  { $limit: 10 }
])
Desafio Final: Dashboard Completo
# 14.1 Crie UMA ÚNICA query de agregação que retorne um dashboard executivo com:

Total de indicações
Total de cerimônias
Total de vencedores
Categoria com mais indicações
Filme mais premiado
Ator/atriz mais indicado(a)
Década com mais premiações
Número de categorias únicas

R:
total_indicacoes: 11134,
  total_cerimonias: 98,
  total_vencedores: 2512,
  categorias_unicas: 116,
  categoria_com_mais_indicacoes: {
    _id: 'DIRECTING',
    indicacoes: 479
  },
  filme_mais_premiado: {
    _id: {
      filme: 'Titanic',
      ano: 1998
    },
    oscars: 11
  },
  ator_mais_indicado: {
    _id: 'Meryl Streep',
    indicacoes: 21
  },
  decada_com_mais_premiacoes: {
    _id: 1950,
    premiacoes: 275
  }
}

db.oscar.aggregate([

  { $match: { ficticio: { $ne: true }, categoria: { $ne: "BEST INTERNATIONAL FEATURE FILM" } } },
  { $addFields: { ganhou: { $cond: [{ $in: ["$vencedor", [1, true]] }, 1, 0] } } },

  { $facet: {

    totais: [
      { $group: {
          _id: null,
          indicacoes: { $sum: 1 },
          vencedores: { $sum: "$ganhou" },
          cerimonias: { $addToSet: "$cerimonia" },
          categorias: { $addToSet: "$categoria" }
      } },
      { $project: {
          _id: 0,
          total_indicacoes: "$indicacoes",
          total_vencedores: "$vencedores",
          total_cerimonias: { $size: "$cerimonias" },
          categorias_unicas: { $size: "$categorias" }
      } }
    ],

    categoria_com_mais_indicacoes: [
      { $group: { _id: "$categoria", indicacoes: { $sum: 1 } } },
      { $sort: { indicacoes: -1 } },
      { $limit: 1 }
    ],

    filme_mais_premiado: [
      { $match: { ganhou: 1, nome_do_filme: { $ne: null } } },
      { $group: { _id: { filme: "$nome_do_filme", ano: "$ano_cerimonia", categoria: "$categoria" } } },
      { $group: { _id: { filme: "$_id.filme", ano: "$_id.ano" }, oscars: { $sum: 1 } } },
      { $sort: { oscars: -1 } },
      { $limit: 1 }
    ],

    ator_mais_indicado: [
      { $match: { categoria: /^ACT(OR|RESS)/ } },
      { $group: { _id: "$nome_do_indicado", indicacoes: { $sum: 1 } } },
      { $sort: { indicacoes: -1 } },
      { $limit: 1 }
    ],

    decada_com_mais_premiacoes: [
      { $match: { ganhou: 1 } },
      { $group: { _id: { ano: "$ano_cerimonia", categoria: "$categoria" } } },
      { $group: {
          _id: { $subtract: ["$_id.ano", { $mod: ["$_id.ano", 10] }] },
          premiacoes: { $sum: 1 }
      } },
      { $sort: { premiacoes: -1 } },
      { $limit: 1 }
    ]
  } },


  { $project: {
      total_indicacoes:  { $arrayElemAt: ["$totais.total_indicacoes", 0] },
      total_cerimonias:  { $arrayElemAt: ["$totais.total_cerimonias", 0] },
      total_vencedores:  { $arrayElemAt: ["$totais.total_vencedores", 0] },
      categorias_unicas: { $arrayElemAt: ["$totais.categorias_unicas", 0] },
      categoria_com_mais_indicacoes: { $arrayElemAt: ["$categoria_com_mais_indicacoes", 0] },
      filme_mais_premiado:           { $arrayElemAt: ["$filme_mais_premiado", 0] },
      ator_mais_indicado:            { $arrayElemAt: ["$ator_mais_indicado", 0] },
      decada_com_mais_premiacoes:    { $arrayElemAt: ["$decada_com_mais_premiacoes", 0] }
  } }
])
