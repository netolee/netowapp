# netowapp
var mysql = require('mysql');
const express = require('express')
const app = express();
// as duas respostas do chalenge estao para visualizacao no brownser na mesma porta mas a partir da raiz em /app1 e /app2 

app.get('/app1', (req, res) => {
  var con = mysql.createConnection({
    host: "35.193.118.123",
    door: "3306",
    database: "ops_challenge",
    user: "challenge",
    password: "#Ch4L13ngE@Pwd#",
  });
  con.connect(function(err) {
    if (err) throw err;
//query feita a partir de aulas na Khan academy, dicas na W3school e mais ajuda de conhecidos
    con.query("select count(*)  as cont, stage_to, weekofyear(update_date) as sem_ano from deals_updates where year(update_date) = 2017 group by stage_to , weekofyear(update_date)" , function (err,result,fields){
      if (err) throw err;
//Bom, este foi o metodo que utilizei, cria dinamicamente o html conforme o resultado da query, usando varios exemplos da internet, porém realmente não consegui fazer o botão filtro funcionar
// entendi que existem outras formas de utilizar,jogando o resultado para um html fixo em extrutura de pastas. Algo que fiquei curioso.  
var html = "<html><button onclick='location.reload()'>Refrescar a Página</button><form><input type=number id='myInput' onkeyup='myFunction()' placeholder='Busca por semana..'><input type='submit' value='Buscar'></form><table id = 'myTable' border=1><tr><td>Estagio</td><td>Semana</td><td>Negócios</td><tr>"
      for (i = 0; i < result.length; i++) {
          html = html + "<tr><td> " + result[i].stage_to  + "</td><td>" +
                                      result[i].sem_ano + "</td><td>" +  
                                      result[i].cont + "</td></tr>"
      }
      html = html + "</table><script>"
      //sobre o fato do filtro não funcionar, acho que deve ser porque eu resolvi fazer os codigos de js e html na mesma página para simplicar, mas quando eu vi já estava assim, 
      //e mesmo tentando de tudo para fazer funcionar, não foi. Mas ainda acho que pode funcionar com tudo junto.
      //abaixo a tentativa do filtro  por semana
      function myFunction() {
        var input, filter, table, tr, td, i, numberValue;
        input = document.getElementById('Semana');
        filter = input.value.toUpperCase();
        table = document.getElementById('myTable');
        tr = table.getElementsByTagNumber('tr');
        for (i = 0; i < tr.length; i++) {
          td = tr[i].getElementsByTagNumber('td')[0];
          if (td) {
            numberValue = td.numberContent || td.innerNumber;
            if (numberValue.toUpperCase().indexOf(filter) > -1) {
              tr[i].style.display = '';
            } else {
              tr[i].style.display = 'none';
            }
          }       
        }
      }
      "</script></html>"
      res.send(html);
    });
  });
  //Acredito que deve haver algum jeito de fazer uma só conexão para as duas respostas, mas achei mas fácil duplicar
});

app.get('/app2', (req, res) => {

  var con = mysql.createConnection({
    host: "35.193.118.123",
    door: "3306",
    database: "ops_challenge",
    user: "challenge",
    password: "#Ch4L13ngE@Pwd#",
  });
  con.connect(function(err) {
    if (err) throw err;
    con.query("select count(*)  as cont, weekofyear(add_date) as sem_ano from deals where year(add_date) = 2017 group by  weekofyear(add_date)" , function (err,result,fields){
      if (err) throw err;

      var html = "<html><table border=1><tr><td>Semana</td><td>Qtde</td><tr>"
      for (i = 0; i < result.length; i++) {
          html = html + "<tr><td> " + 
                                      result[i].sem_ano + "</td><td>" +  
                                      result[i].cont + "</td></tr>"
      }
      html = html + "</table></html>"
      res.send(html);
    });
  });
});

app.listen(3010, () => {
  console.log('Example app listening on port 3306!')
});
