public class API {
    /**
     * 
     */
    public static void main(package com.example.coisasdecasa;
}
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class CoisasDeCasaApplication {

	public static void main(String[] args) {
		SpringApplication.run(CoisasDeCasaApplication.class, args);
	}

}

package com.example.coisasdecasa.model;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class Tarefa {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String descricao;

    private Boolean concluida;

    public Tarefa() {
    }

    public Tarefa(String descricao) {
        this.descricao = descricao;
        this.concluida = false;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getDescricao() {
        return descricao;
    }

    public void setDescricao(String descricao) {
        this.descricao = descricao;
    }

    public Boolean getConcluida() {
        return concluida;
    }

    public void setConcluida(Boolean concluida) {
        this.concluida = concluida;
    }
}

package com.example.coisasdecasa.model;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class ListaDeCompras {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String nome;

    private String itens;

    public ListaDeCompras() {
    }

    public ListaDeCompras(String nome, String itens) {
        this.nome = nome;
        this.itens = itens;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getNome() {
        return nome;
    }

    public void setNome(String nome) {
        this.nome = nome;
    }

    public String getItens() {
        return itens;
    }

    public void setItens(String itens) {
        this.itens = itens;
    }
}

package com.example.coisasdecasa.repository;

import com.example.coisasdecasa.model.Tarefa;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface TarefaRepository extends JpaRepository<Tarefa, Long> {

}

package com.example.coisasdecasa.repository;

import com.example.coisasdecasa.model.ListaDeCompras;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface ListaDeComprasRepository extends JpaRepository<ListaDeCompras, Long> {

}

package com.example.coisasdecasa.service;

import com.example.coisasdecasa.model.Tarefa;
import com.example.coisasdecasa.repository.TarefaRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class TarefaService {

    @Autowired
    private TarefaRepository tarefaRepository;

    public List<Tarefa> listarTarefas() {
        return tarefaRepository.findAll();
    }

    public Tarefa buscarTarefaPorId(Long id) {
        return tarefaRepository.findById(id).orElse(null);
    }

    public Tarefa criarTarefa(Tarefa tarefa) {
        return tarefaRepository.save(tarefa);
    }

    public Tarefa atualizarTarefa(Tarefa tarefa) {
        return tarefaRepository.save(tarefa);
    }

    public void deletarTarefa(Long id) {
        tarefaRepository.deleteById(id);
    }
}

package com.example.coisasdecasa.service;

import com.example.coisasdecasa.model.ListaDeCompras;
import com.example.coisasdecasa.repository.ListaDeComprasRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class ListaDeComprasService {

    @Autowired
    private ListaDeComprasRepository listaDeComprasRepository;

    public List<ListaDeCompras> listarListasDeCompras() {
        return listaDeComprasRepository.findAll();
    }

    public ListaDeCompras buscarListaDeComprasPorId(Long id) {
        return listaDeComprasRepository.findById(id).orElse(null);
    }

    public ListaDeCompras criarListaDeCompras(ListaDeCompras listaDeCompras) {
        return listaDeComprasRepository.save(listaDeCompras);
    }

    public ListaDeCompras atualizarListaDeCompras(ListaDeCompras listaDeCompras) {
        return listaDeComprasRepository.save(listaDeCompras);
    }

    public void deletarListaDeCompras(Long id) {
        listaDeComprasRepository.deleteById(id);
    }
}

package com.example.coisasdecasa.controller;

import com.example.coisasdecasa.model.Tarefa;
import com.example.coisasdecasa.service.TarefaService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/tarefas")
public class TarefaController {

    @Autowired
    private TarefaService tarefaService;

    @GetMapping
    public ResponseEntity<List<Tarefa>> listarTarefas() {
        List<Tarefa> tarefas = tarefaService.listarTarefas();
        return ResponseEntity.ok(tarefas);
    }

    @GetMapping("/{id}")
    public ResponseEntity<Tarefa> buscarTarefaPorId(@PathVariable Long id) {
        Tarefa tarefa = tarefaService.buscarTarefaPorId(id);
        if (tarefa == null) {
            return ResponseEntity.notFound().build();
        }
        return ResponseEntity.ok(tarefa);
    }

    @PostMapping
    public ResponseEntity<Tarefa> criarTarefa(@RequestBody Tarefa tarefa) {
        Tarefa novaTarefa = tarefaService.criarTarefa(tarefa);
        return ResponseEntity.status(HttpStatus.CREATED).body(novaTarefa);
    }

    @PutMapping("/{id}")
    public ResponseEntity<Tarefa> atualizarTarefa(@PathVariable Long id, @RequestBody Tarefa tarefa) {
        Tarefa tarefaAtual = tarefaService.buscarTarefaPorId(id);
        if (tarefaAtual == null) {
            return ResponseEntity.notFound().build();
        }
        tarefaAtual.setDescricao(tarefa.getDescricao());
        tarefaAtual.setConcluida(tarefa.getConcluida());
        Tarefa tarefaAtualizada = tarefaService.atualizarTarefa(tarefaAtual);
        return ResponseEntity.ok(tarefaAtualizada);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deletarTarefa(@PathVariable Long id) {
        Tarefa tarefa = tarefaService.buscarTarefaPorId(id);
        if (tarefa == null) {
            return ResponseEntity.notFound().build();
        }
        tarefaService.deletarTarefa(id);
        return ResponseEntity.noContent().build();
    }
}

package com.example.coisasdecasa.controller;

import com.example.coisasdecasa.model.ListaDeCompras;
import com.example.coisasdecasa.service.ListaDeComprasService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/listas-de-compras")
public class ListaDeComprasController {

    @Autowired
    private ListaDeComprasService listaDeComprasService;

    @GetMapping
    public ResponseEntity<List<ListaDeCompras>> listarListasDeCompras() {
        List<ListaDeCompras> listasDeCompras = listaDeComprasService.listarListasDeCompras();
        return ResponseEntity.ok(listasDeCompras);
    }

    @GetMapping("/{id}")
    public ResponseEntity<ListaDeCompras> buscarListaDeComprasPorId(@PathVariable Long id) {
        ListaDeCompras listaDeCompras = listaDeComprasService.buscarListaDeComprasPorId(id);
        if (listaDeCompras == null) {
            return ResponseEntity.notFound().build();
        }
        return ResponseEntity.ok(listaDeCompras);
    }

    @PostMapping
    public ResponseEntity<ListaDeCompras> criarListaDeCompras(@RequestBody ListaDeCompras listaDeCompras) {
        ListaDeCompras novaListaDeCompras = listaDeComprasService.criarListaDeCompras(listaDeCompras);
        return ResponseEntity.status(HttpStatus.CREATED).body(novaListaDeCompras);
    }

    @PutMapping("/{id}")
    public ResponseEntity<ListaDeCompras> atualizarListaDeCompras(@PathVariable Long id, @RequestBody ListaDeCompras listaDeCompras) {
        ListaDeCompras listaDeComprasAtual = listaDeComprasService.buscarListaDeComprasPorId(id);
        if (listaDeComprasAtual == null) {
            return ResponseEntity.notFound().build();
        }
        listaDeComprasAtual.setNome(listaDeCompras.getNome());
        listaDeComprasAtual.setItens(listaDeCompras.getItens());
        ListaDeCompras listaDeComprasAtualizada = listaDeComprasService.atualizarListaDeCompras(listaDeComprasAtual);
        return ResponseEntity.ok(listaDeComprasAtualizada);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deletarListaDeCompras(@PathVariable Long id) {
        ListaDeCompras listaDeCompras = listaDeCompra
<|

