<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Cadastro de Clientes</title>
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  :root {
    --primary:   #0C769E;
    --primary-d: #095e80;
    --primary-l: #DAEEF3;
    --accent:    #31AFBE;
    --white:     #FFFFFF;
    --bg:        #F3F3F3;
    --text:      #1a2e38;
    --text-2:    #5a7380;
    --text-3:    #9bb5bf;
    --border:    #DAEEF3;
    --red:       #DC2626;
    --red-light: #FEF2F2;
    --radius:    10px;
    --radius-sm: 7px;
  }
  body { font-family: 'Segoe UI', system-ui, -apple-system, sans-serif; background: var(--bg); color: var(--text); min-height: 100vh; padding: 2rem 1rem 4rem; }

  .page-header { text-align: center; margin-bottom: 2rem; }
  .logo-circle { width: 56px; height: 56px; border-radius: 50%; background: var(--primary); display: flex; align-items: center; justify-content: center; margin: 0 auto 1rem; }
  .logo-circle svg { width: 28px; height: 28px; stroke: white; fill: none; stroke-width: 2; stroke-linecap: round; }
  .page-header h1 { font-size: 1.65rem; font-weight: 700; color: var(--primary); }
  .page-header p  { font-size: .9rem; color: var(--text-2); margin-top: 4px; }

  .progress-wrap { max-width: 640px; margin: 0 auto 2rem; display: flex; align-items: center; }
  .step-node { display: flex; flex-direction: column; align-items: center; gap: 6px; flex: none; }
  .step-bubble { width: 36px; height: 36px; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 13px; font-weight: 600; border: 2px solid var(--border); background: var(--white); color: var(--text-3); transition: all .3s; }
  .step-bubble.active { background: var(--primary); border-color: var(--primary); color: white; }
  .step-bubble.done   { background: var(--accent); border-color: var(--accent); color: white; }
  .step-bubble.done span { display: none; }
  .step-name { font-size: 11px; color: var(--text-3); font-weight: 500; white-space: nowrap; }
  .step-name.active { color: var(--primary); font-weight: 600; }
  .step-connector { flex: 1; height: 2px; background: var(--border); margin-bottom: 20px; transition: background .3s; }
  .step-connector.done { background: var(--accent); }

  .card { max-width: 640px; margin: 0 auto; background: var(--white); border-radius: 16px; border: 1px solid var(--border); overflow: hidden; }
  .card-header { padding: 1.5rem 2rem 1.25rem; border-bottom: 1px solid var(--border); background: linear-gradient(to right, var(--primary-l), var(--white)); }
  .card-header h2 { font-size: 1.1rem; font-weight: 700; color: var(--primary); }
  .card-header p  { font-size: .83rem; color: var(--text-2); margin-top: 3px; }
  .card-body   { padding: 1.75rem 2rem; }
  .card-footer { padding: 1.25rem 2rem; border-top: 1px solid var(--border); display: flex; align-items: center; justify-content: space-between; background: var(--bg); }

  .grid { display: grid; gap: 18px; }
  .grid-2 { grid-template-columns: 1fr 1fr; }
  .grid-3 { grid-template-columns: 1fr 1fr 1fr; }
  .field { display: flex; flex-direction: column; gap: 6px; position: relative; }
  .field label { font-size: 11px; font-weight: 700; text-transform: uppercase; letter-spacing: .06em; color: var(--text-2); }
  .req { color: var(--red); }
  .opt { font-size: 10px; font-weight: 500; background: var(--primary-l); color: var(--primary); padding: 2px 7px; border-radius: 20px; text-transform: none; letter-spacing: 0; margin-left: 4px; }
  input, select { height: 42px; padding: 0 12px; font-size: 14px; border: 1.5px solid var(--border); border-radius: var(--radius-sm); background: var(--white); color: var(--text); outline: none; transition: border-color .2s, box-shadow .2s; width: 100%; font-family: inherit; }
  input:focus, select:focus { border-color: var(--primary); box-shadow: 0 0 0 3px rgba(12,118,158,.12); }
  input::placeholder { color: var(--text-3); }
  select { appearance: none; background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='12' height='12' viewBox='0 0 12 12'%3E%3Cpath fill='%235a7380' d='M6 8.5L1 3.5h10z'/%3E%3C/svg%3E"); background-repeat: no-repeat; background-position: right 12px center; padding-right: 32px; }
  .field.invalid input, .field.invalid select, .field.invalid .ac-input { border-color: var(--red) !important; background: var(--red-light) !important; }
  .errmsg { font-size: 11px; color: var(--red); display: none; }
  .field.invalid .errmsg { display: block; }

  .toggle-row { display: flex; border: 1.5px solid var(--border); border-radius: var(--radius-sm); overflow: hidden; height: 42px; }
  .toggle-row input[type=radio] { display: none; }
  .toggle-row label { flex: 1; display: flex; align-items: center; justify-content: center; font-size: 13px; font-weight: 500; color: var(--text-2); cursor: pointer; border-right: 1.5px solid var(--border); transition: all .15s; text-transform: none; letter-spacing: 0; }
  .toggle-row label:last-of-type { border-right: none; }
  .toggle-row input:checked + label { background: var(--primary-l); color: var(--primary); font-weight: 600; }

  .cep-row { display: grid; grid-template-columns: 1fr auto; gap: 8px; }
  .cep-btn { height: 42px; padding: 0 16px; font-size: 13px; font-weight: 600; border: 1.5px solid var(--border); border-radius: var(--radius-sm); background: var(--bg); color: var(--text-2); cursor: pointer; transition: all .15s; font-family: inherit; white-space: nowrap; }
  .cep-btn:hover { background: var(--primary-l); border-color: var(--primary); color: var(--primary); }

  /* Autocomplete */
  .ac-wrap { position: relative; }
  .ac-input { height: 42px; padding: 0 12px; font-size: 14px; border: 1.5px solid var(--border); border-radius: var(--radius-sm); background: var(--white); color: var(--text); outline: none; transition: border-color .2s, box-shadow .2s; width: 100%; font-family: inherit; }
  .ac-input:focus { border-color: var(--primary); box-shadow: 0 0 0 3px rgba(12,118,158,.12); }
  .ac-input::placeholder { color: var(--text-3); }
  .ac-list { position: absolute; top: calc(100% + 4px); left: 0; right: 0; background: var(--white); border: 1.5px solid var(--primary); border-radius: var(--radius-sm); z-index: 200; max-height: 210px; overflow-y: auto; display: none; box-shadow: 0 4px 16px rgba(12,118,158,.1); }
  .ac-list.open { display: block; }
  .ac-item { padding: 10px 14px; font-size: 13px; color: var(--text); cursor: pointer; border-bottom: 1px solid var(--primary-l); transition: background .1s; }
  .ac-item:last-child { border-bottom: none; }
  .ac-item:hover, .ac-item.hi { background: var(--primary-l); color: var(--primary); font-weight: 500; }
  .ac-item.hint { color: var(--accent); font-style: italic; cursor: default; }
  .ac-item.hint:hover { background: transparent; color: var(--accent); font-weight: 400; }
  .ac-list::-webkit-scrollbar { width: 5px; }
  .ac-list::-webkit-scrollbar-thumb { background: var(--border); border-radius: 3px; }

  /* Serviço cards */
  .servico-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
  .servico-card { position: relative; cursor: pointer; }
  .servico-card input[type=radio] { display: none; }
  .servico-label { display: flex; flex-direction: column; gap: 6px; padding: 1rem 1.1rem; border: 1.5px solid var(--border); border-radius: var(--radius); background: var(--white); cursor: pointer; transition: all .2s; }
  .servico-card input:checked + .servico-label { border-color: var(--primary); background: var(--primary-l); }
  .servico-label:hover { border-color: var(--accent); background: var(--primary-l); }
  .servico-icon { width: 36px; height: 36px; border-radius: 8px; background: var(--primary-l); display: flex; align-items: center; justify-content: center; margin-bottom: 2px; transition: background .2s; }
  .servico-card input:checked + .servico-label .servico-icon { background: var(--primary); }
  .servico-icon svg { width: 18px; height: 18px; stroke: var(--primary); fill: none; stroke-width: 2; stroke-linecap: round; stroke-linejoin: round; transition: stroke .2s; }
  .servico-card input:checked + .servico-label .servico-icon svg { stroke: white; }
  .servico-nome { font-size: 13px; font-weight: 700; color: var(--text); }
  .servico-desc { font-size: 11px; color: var(--text-2); line-height: 1.5; }
  .servico-card input:checked + .servico-label .servico-nome { color: var(--primary); }
  .check-mark { position: absolute; top: 10px; right: 10px; width: 18px; height: 18px; border-radius: 50%; border: 1.5px solid var(--border); background: var(--white); display: flex; align-items: center; justify-content: center; transition: all .2s; }
  .servico-card input:checked + .servico-label .check-mark { background: var(--primary); border-color: var(--primary); }
  .check-mark svg { width: 10px; height: 10px; stroke: white; fill: none; stroke-width: 2.5; stroke-linecap: round; opacity: 0; transition: opacity .2s; }
  .servico-card input:checked + .servico-label .check-mark svg { opacity: 1; }
  @media (max-width: 420px) { .servico-grid { grid-template-columns: 1fr; } }

  /* Privacy */
  .privacy-note { background: var(--primary-l); border-radius: var(--radius-sm); border-left: 3px solid var(--accent); padding: .85rem 1rem; font-size: 12px; color: var(--text-2); line-height: 1.6; }
  .privacy-note strong { color: var(--primary); display: block; margin-bottom: 3px; font-size: 12px; }

  .step-count { font-size: 12px; color: var(--text-3); font-weight: 500; }
  .btn-back { height: 40px; padding: 0 20px; font-size: 14px; font-weight: 500; border: 1.5px solid var(--border); border-radius: var(--radius-sm); background: var(--white); color: var(--text-2); cursor: pointer; transition: all .15s; font-family: inherit; }
  .btn-back:hover { background: var(--primary-l); border-color: var(--primary); color: var(--primary); }
  .btn-next { height: 40px; padding: 0 24px; font-size: 14px; font-weight: 600; border: none; border-radius: var(--radius-sm); background: var(--primary); color: white; cursor: pointer; transition: all .15s; font-family: inherit; }
  .btn-next:hover { background: var(--primary-d); }
  .btn-next:active { transform: scale(.98); }
  .btn-next:disabled { background: var(--text-3); cursor: not-allowed; transform: none; }

  .panel { display: none; }
  .panel.active { display: block; }

  .success-screen { text-align: center; padding: 3rem 2rem; }
  .success-icon { width: 72px; height: 72px; border-radius: 50%; background: var(--primary-l); border: 2px solid var(--accent); display: flex; align-items: center; justify-content: center; margin: 0 auto 1.5rem; }
  .success-icon svg { width: 32px; height: 32px; stroke: var(--primary); fill: none; stroke-width: 2.5; stroke-linecap: round; stroke-linejoin: round; }
  .success-screen h2 { font-size: 1.5rem; font-weight: 700; color: var(--primary); margin-bottom: 8px; }
  .success-screen .sub { font-size: 14px; color: var(--text-2); line-height: 1.7; max-width: 380px; margin: 0 auto; }
  .email-badge { display: inline-flex; align-items: center; gap: 6px; font-size: 13px; color: var(--primary); background: var(--primary-l); padding: 8px 18px; border-radius: 20px; margin-top: 14px; border: 1px solid var(--border); }
  .record-summary { background: var(--bg); border: 1px solid var(--border); border-radius: var(--radius); padding: 1rem 1.25rem; margin: 1.5rem auto 0; max-width: 420px; text-align: left; }
  .record-row { display: flex; justify-content: space-between; font-size: 13px; padding: 6px 0; border-bottom: 1px solid var(--border); }
  .record-row:last-child { border-bottom: none; }
  .record-row span:first-child { color: var(--text-2); }
  .record-row span:last-child  { font-weight: 600; color: var(--primary); max-width: 220px; text-align: right; }
  .btn-novo { display: inline-flex; align-items: center; gap: 8px; height: 42px; padding: 0 24px; font-size: 14px; font-weight: 600; margin-top: 2rem; border: none; border-radius: var(--radius-sm); background: var(--primary); color: white; cursor: pointer; transition: all .15s; font-family: inherit; }
  .btn-novo:hover { background: var(--primary-d); }

  @keyframes spin { to { transform: rotate(360deg); } }
  .spinner { width: 15px; height: 15px; border: 2.5px solid rgba(255,255,255,.4); border-top-color: white; border-radius: 50%; animation: spin .7s linear infinite; display: inline-block; vertical-align: middle; margin-right: 4px; }

  @media (max-width: 560px) {
    .grid-2, .grid-3 { grid-template-columns: 1fr; }
    .card-body { padding: 1.25rem; } .card-header { padding: 1.25rem; } .card-footer { padding: 1rem 1.25rem; }
    .step-name { display: none; }
  }
</style>
</head>
<body>

<div class="page-header">
  <div class="logo-circle">
    <svg viewBox="0 0 24 24"><path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"/><circle cx="12" cy="7" r="4"/></svg>
  </div>
  <h1>Cadastro de Clientes</h1>
  <p>Preencha os dados para registrar um novo cliente</p>
</div>

<div class="progress-wrap">
  <div class="step-node"><div class="step-bubble active" id="sc1"><span>1</span></div><div class="step-name active" id="sl1">Pessoal</div></div>
  <div class="step-connector" id="ln1"></div>
  <div class="step-node"><div class="step-bubble" id="sc2"><span>2</span></div><div class="step-name" id="sl2">Endereço</div></div>
  <div class="step-connector" id="ln2"></div>
  <div class="step-node"><div class="step-bubble" id="sc3"><span>3</span></div><div class="step-name" id="sl3">Contato</div></div>
  <div class="step-connector" id="ln3"></div>
  <div class="step-node"><div class="step-bubble" id="sc4"><span>4</span></div><div class="step-name" id="sl4">Extras</div></div>
</div>

<div class="card">

  <!-- ETAPA 1 -->
  <div class="panel active" id="p1">
    <div class="card-header"><h2>Informações Pessoais</h2><p>Dados de identificação do cliente</p></div>
    <div class="card-body">
      <div class="grid">
        <div class="field" id="f-servico">
          <label>Serviço contratado <span class="req">*</span></label>
          <div class="servico-grid">
            <div class="servico-card">
              <input type="radio" name="servico" id="srv-pf" value="Planejamento Financeiro">
              <label class="servico-label" for="srv-pf">
                <div class="servico-icon">
                  <svg viewBox="0 0 24 24"><path d="M12 2L2 7l10 5 10-5-10-5z"/><path d="M2 17l10 5 10-5"/><path d="M2 12l10 5 10-5"/></svg>
                </div>
                <div class="servico-nome">Planejamento Financeiro</div>
                <div class="check-mark"><svg viewBox="0 0 10 10"><polyline points="1.5 5 4 7.5 8.5 2.5"/></svg></div>
              </label>
            </div>
            <div class="servico-card">
              <input type="radio" name="servico" id="srv-ci" value="Consultoria de Investimentos">
              <label class="servico-label" for="srv-ci">
                <div class="servico-icon">
                  <svg viewBox="0 0 24 24"><polyline points="22 12 18 12 15 21 9 3 6 12 2 12"/></svg>
                </div>
                <div class="servico-nome">Consultoria de Investimentos</div>
                <div class="check-mark"><svg viewBox="0 0 10 10"><polyline points="1.5 5 4 7.5 8.5 2.5"/></svg></div>
              </label>
            </div>
          </div>
          <div class="errmsg">Selecione o serviço contratado</div>
        </div>
        <div class="field" id="f-nome"><label>Nome completo <span class="req">*</span></label><input id="nome" placeholder="Nome e sobrenome completo"><div class="errmsg">Campo obrigatório</div></div>
        <div class="grid grid-2">
          <div class="field" id="f-cpf"><label>CPF <span class="req">*</span></label><input id="cpf" placeholder="000.000.000-00" maxlength="14"><div class="errmsg">CPF inválido</div></div>
          <div class="field" id="f-rg"><label>RG <span class="req">*</span></label><input id="rg" placeholder="00.000.000-0"><div class="errmsg">Campo obrigatório</div></div>
        </div>
        <div class="grid grid-2">
          <div class="field" id="f-nasc"><label>Nascimento <span class="req">*</span></label><input type="date" id="nasc"><div class="errmsg">Campo obrigatório</div></div>
          <div class="field" id="f-form">
            <label>Formação Profissional <span class="req">*</span></label>
            <select id="form">
              <option value="">Selecione</option>
              <option>Ensino Fundamental</option>
              <option>Ensino Médio</option>
              <option>Técnico</option>
              <option>Tecnólogo</option>
              <option>Superior / Graduação</option>
              <option>Pós-graduação / Especialização</option>
              <option>MBA</option>
              <option>Mestrado</option>
              <option>Doutorado</option>
              <option>Pós-Doutorado</option>
            </select>
            <div class="errmsg">Campo obrigatório</div>
          </div>
        </div>
        <div class="field" id="f-ecivil"><label>Estado Civil <span class="req">*</span></label>
          <select id="ecivil"><option value="">Selecione</option><option>Solteiro(a)</option><option>Casado(a)</option><option>Divorciado(a)</option><option>Viúvo(a)</option><option>União estável</option></select>
          <div class="errmsg">Campo obrigatório</div>
        </div>
        <div class="grid grid-2">
          <div class="field" id="f-profissao">
            <label>Profissão <span class="req">*</span></label>
            <div class="ac-wrap">
              <input class="ac-input" id="profissao" placeholder="Ex: Médico, Engenheiro, Professor..." autocomplete="off">
              <div class="ac-list" id="prof-list"></div>
            </div>
            <div class="errmsg">Campo obrigatório</div>
          </div>
          <div class="field" id="f-ativ">
            <label>Atividade / Função <span class="req">*</span></label>
            <input id="ativ" placeholder="Descreva sua atividade ou função">
            <div class="errmsg">Campo obrigatório</div>
          </div>
        </div>
      </div>
    </div>
    <div class="card-footer"><div class="step-count">Etapa 1 de 4</div><button class="btn-next" onclick="goNext(1)">Continuar →</button></div>
  </div>

  <!-- ETAPA 2 -->
  <div class="panel" id="p2">
    <div class="card-header"><h2>Endereço</h2><p>Informe o endereço completo</p></div>
    <div class="card-body">
      <div class="grid">
        <div class="grid grid-2">
          <div class="field" id="f-cep"><label>CEP <span class="req">*</span></label>
            <div class="cep-row"><input id="cep" placeholder="00000-000" maxlength="9"><button type="button" class="cep-btn" onclick="buscarCep()">Buscar</button></div>
            <div class="errmsg">CEP inválido</div></div>
          <div class="field" id="f-uf"><label>Estado (UF) <span class="req">*</span></label>
            <select id="uf"><option value="">Selecione</option><option>AC</option><option>AL</option><option>AP</option><option>AM</option><option>BA</option><option>CE</option><option>DF</option><option>ES</option><option>GO</option><option>MA</option><option>MT</option><option>MS</option><option>MG</option><option>PA</option><option>PB</option><option>PR</option><option>PE</option><option>PI</option><option>RJ</option><option>RN</option><option>RS</option><option>RO</option><option>RR</option><option>SC</option><option selected>SP</option><option>SE</option><option>TO</option></select>
            <div class="errmsg">Campo obrigatório</div></div>
        </div>
        <div class="grid grid-2">
          <div class="field" id="f-cidade"><label>Cidade <span class="req">*</span></label><input id="cidade" placeholder="Nome da cidade"><div class="errmsg">Campo obrigatório</div></div>
          <div class="field" id="f-bairro"><label>Bairro <span class="req">*</span></label><input id="bairro" placeholder="Nome do bairro"><div class="errmsg">Campo obrigatório</div></div>
        </div>
        <div class="field" id="f-rua"><label>Logradouro <span class="req">*</span></label><input id="rua" placeholder="Rua, Avenida, Alameda..."><div class="errmsg">Campo obrigatório</div></div>
        <div class="grid grid-2">
          <div class="field" id="f-num"><label>Número <span class="req">*</span></label><input id="num" placeholder="Ex: 123"><div class="errmsg">Campo obrigatório</div></div>
          <div class="field" id="f-comp"><label>Complemento <span class="req">*</span></label><input id="comp" placeholder="Apto, sala, bloco..."><div class="errmsg">Campo obrigatório</div></div>
        </div>
      </div>
    </div>
    <div class="card-footer"><button class="btn-back" onclick="goBack(2)">← Voltar</button><div class="step-count">Etapa 2 de 4</div><button class="btn-next" onclick="goNext(2)">Continuar →</button></div>
  </div>

  <!-- ETAPA 3 -->
  <div class="panel" id="p3">
    <div class="card-header"><h2>Contato</h2><p>Como entrar em contato com o cliente</p></div>
    <div class="card-body">
      <div class="grid">
        <div class="field" id="f-tel">
          <label>Telefone / WhatsApp <span class="req">*</span></label>
          <input id="tel" type="tel" placeholder="(00) 00000-0000" maxlength="15">
          <div class="errmsg">Telefone inválido</div>
        </div>
        <div class="field" id="f-email">
          <label>E-mail <span class="req">*</span></label>
          <input id="email" type="email" placeholder="cliente@email.com">
          <div class="errmsg">E-mail inválido</div>
        </div>
      </div>
    </div>
    <div class="card-footer"><button class="btn-back" onclick="goBack(3)">← Voltar</button><div class="step-count">Etapa 3 de 4</div><button class="btn-next" onclick="goNext(3)">Continuar →</button></div>
  </div>

  <!-- ETAPA 4 -->
  <div class="panel" id="p4">
    <div class="card-header"><h2>Informações Adicionais</h2><p>Todos os campos abaixo são opcionais</p></div>
    <div class="card-body">
      <div class="grid">
        <div class="field"><label>Fumante <span class="opt">opcional</span></label>
          <div class="toggle-row"><input type="radio" name="fum" id="fum-s" value="Sim"><label for="fum-s">Sim</label><input type="radio" name="fum" id="fum-n" value="Não"><label for="fum-n">Não</label></div></div>
        <div class="field"><label>Politicamente exposto (PEP) <span class="opt">opcional</span></label>
          <div class="toggle-row"><input type="radio" name="pep" id="pep-s" value="Sim"><label for="pep-s">Sim</label><input type="radio" name="pep" id="pep-n" value="Não"><label for="pep-n">Não</label></div></div>
        <div class="grid grid-2">
          <div class="field"><label>Peso <span class="opt">opcional</span></label><input id="peso" placeholder="Ex: 70 kg"></div>
          <div class="field"><label>Altura <span class="opt">opcional</span></label><input id="altura" placeholder="Ex: 1,75 m"></div>
        </div>
        <div class="privacy-note">
          <strong>Aviso de privacidade</strong>
          Os dados informados nesta etapa são utilizados exclusivamente para avaliação de risco pessoal e elaboração de cotações. Suas informações são tratadas com total sigilo e não serão compartilhadas com terceiros.
        </div>
      </div>
    </div>
    <div class="card-footer">
      <button class="btn-back" onclick="goBack(4)">← Voltar</button>
      <div class="step-count">Etapa 4 de 4</div>
      <button class="btn-next" id="btnFinalizar" onclick="finalizar()">✓ Finalizar Cadastro</button>
    </div>
  </div>

  <!-- SUCESSO -->
  <div class="panel" id="p5">
    <div class="success-screen">
      <div class="success-icon"><svg viewBox="0 0 24 24"><polyline points="20 6 9 17 4 12"/></svg></div>
      <h2>Cadastro realizado!</h2>
      <p class="sub">Seus dados foram registrados com sucesso. Em breve entraremos em contato.</p>
      <div class="email-badge">
        <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><rect x="2" y="4" width="20" height="16" rx="2"/><polyline points="2,4 12,13 22,4"/></svg>
        E-mail de confirmação enviado
      </div>
      <div class="record-summary" id="resumo"></div>
      <button class="btn-novo" onclick="novoCadastro()">+ Novo cadastro</button>
    </div>
  </div>

</div>

<script>
const SCRIPT_URL = "https://script.google.com/macros/s/AKfycbzvzBorjYi7HZaEwv0MXr7ucGC6OWKc2XwlTCYRDcEKGmBwJPUqeook0b2VcRtPuXNh/exec";

const FORMACOES = ["Ensino Fundamental","Ensino Médio","Técnico","Tecnólogo","Graduação / Bacharel","Licenciatura","Pós-graduação / Especialização","MBA","Mestrado","Doutorado","Pós-Doutorado"];

const ATIVIDADES = [
  "Analista","Analista de Dados","Analista de Marketing","Analista de Negócios",
  "Analista de RH","Analista de Sistemas","Analista de TI","Analista Financeiro",
  "Assistente Administrativo","Assistente Comercial","Assistente de Projetos",
  "Auditor","Auxiliar Administrativo","Auxiliar Contábil",
  "Consultor","Consultor de Negócios","Consultor Financeiro","Consultor de TI",
  "Coordenador","Coordenador Comercial","Coordenador de Marketing",
  "Coordenador de Projetos","Coordenador de RH","Coordenador Financeiro",
  "Desenvolvedor","Desenvolvedor Back-end","Desenvolvedor Front-end","Desenvolvedor Full Stack",
  "Designer","Designer Gráfico","Designer de Produto","Designer UX/UI",
  "Diretor","Diretor Comercial","Diretor de Marketing","Diretor de Operações",
  "Diretor de TI","Diretor Financeiro","Diretor de RH",
  "Engenheiro","Engenheiro de Software","Engenheiro de Dados","Engenheiro de Produção",
  "Especialista","Especialista em Marketing","Especialista em RH","Especialista Financeiro",
  "Gerente","Gerente Comercial","Gerente de Contas","Gerente de Marketing",
  "Gerente de Operações","Gerente de Projetos","Gerente de RH","Gerente Financeiro",
  "Gestor","Gestor de Projetos","Gestor de TI","Gestor Financeiro",
  "Líder Técnico","Líder de Equipe",
  "Professor","Pesquisador","Profissional de Saúde",
  "Scrum Master","Product Owner","Product Manager",
  "Supervisor","Supervisor Comercial","Supervisor de Operações",
  "Técnico","Técnico de TI","Técnico de Suporte",
  "Vendedor","Representante Comercial",
  "CEO","CFO","COO","CTO","Sócio","Proprietário","Empreendedor",
  "Aposentado","Estudante","Desempregado"
];

// ── Autocomplete genérico ─────────────────────────────────────
function initAC(inputId, listId, items, hiddenId) {
  const inp  = document.getElementById(inputId);
  const list = document.getElementById(listId);
  let hi = -1;

  function render(q) {
    list.innerHTML = ''; hi = -1;
    if (!q.trim()) { list.classList.remove('open'); return; }
    const matches = items.filter(a => a.toLowerCase().includes(q.toLowerCase())).slice(0, 9);
    if (!matches.length) {
      const el = document.createElement('div');
      el.className = 'ac-item hint';
      el.textContent = 'Não encontrado — continue digitando livremente';
      list.appendChild(el);
    } else {
      matches.forEach(m => {
        const el = document.createElement('div');
        el.className = 'ac-item';
        el.textContent = m;
        el.addEventListener('mousedown', ev => {
          ev.preventDefault();
          inp.value = m;
          if (hiddenId) document.getElementById(hiddenId).value = m;
          list.classList.remove('open');
        });
        list.appendChild(el);
      });
      const hint = document.createElement('div');
      hint.className = 'ac-item hint';
      hint.textContent = 'Não listado? Continue digitando livremente';
      list.appendChild(hint);
    }
    list.classList.add('open');
  }

  inp.addEventListener('input', () => {
    if (hiddenId) document.getElementById(hiddenId).value = inp.value;
    render(inp.value);
  });
  inp.addEventListener('focus',  () => { if (inp.value) render(inp.value); });
  inp.addEventListener('blur',   () => setTimeout(() => list.classList.remove('open'), 160));
  inp.addEventListener('keydown', e => {
    const its = [...list.querySelectorAll('.ac-item:not(.hint)')];
    if (e.key === 'ArrowDown') { hi = Math.min(hi+1, its.length-1); its.forEach((it,i) => it.classList.toggle('hi', i===hi)); e.preventDefault(); }
    if (e.key === 'ArrowUp')   { hi = Math.max(hi-1, 0); its.forEach((it,i) => it.classList.toggle('hi', i===hi)); e.preventDefault(); }
    if (e.key === 'Enter' && hi >= 0) {
      inp.value = its[hi].textContent;
      if (hiddenId) document.getElementById(hiddenId).value = its[hi].textContent;
      list.classList.remove('open'); e.preventDefault();
    }
    if (e.key === 'Escape') list.classList.remove('open');
  });
}

const PROFISSOES = ["Administrador","Advogado","Agrônomo","Arquiteto","Assistente Social","Biólogo","Biomédico","Contador","Dentista / Odontólogo","Designer","Economista","Enfermeiro","Engenheiro","Engenheiro Civil","Engenheiro de Software","Engenheiro Elétrico","Engenheiro Mecânico","Farmacêutico","Filósofo","Físico","Fisioterapeuta","Fonoaudiólogo","Geógrafo","Geólogo","Historiador","Jornalista","Matemático","Médico","Médico Veterinário","Nutricionista","Pedagogo","Profissional de Educação Física","Psicólogo","Publicitário","Químico","Relações Públicas","Secretário Executivo","Sociólogo","Tecnólogo","Terapeuta Ocupacional","Tradutor"];

initAC('profissao', 'prof-list', PROFISSOES, null);

// ── Máscaras ──────────────────────────────────────────────────
function mask(id, pat) {
  document.getElementById(id).addEventListener('input', function () {
    let v = this.value.replace(/\D/g,''), i=0, r='';
    for (let c of pat) { if (i>=v.length) break; r += c==='#'?v[i++]:c; }
    this.value = r;
  });
}
mask('cpf', '###.###.###-##');
mask('cep', '#####-###');

// Telefone: (DD) XXXXX-XXXX ou (DD) XXXX-XXXX — sem DDI
document.getElementById('tel').addEventListener('input', function () {
  let d = this.value.replace(/\D/g,'').slice(0,11);
  if (!d) { this.value=''; return; }
  if (d.length <= 2)        this.value = '(' + d;
  else if (d.length <= 6)   this.value = '(' + d.slice(0,2) + ') ' + d.slice(2);
  else if (d.length <= 10)  this.value = '(' + d.slice(0,2) + ') ' + d.slice(2,6) + '-' + d.slice(6);
  else                      this.value = '(' + d.slice(0,2) + ') ' + d.slice(2,7) + '-' + d.slice(7);
});

// ── Progress ──────────────────────────────────────────────────
let cur = 1;
function setStep(n) {
  document.querySelectorAll('.panel').forEach((p,i) => p.classList.toggle('active', i+1===n));
  [1,2,3,4].forEach(i => {
    const c = document.getElementById('sc'+i), l = document.getElementById('sl'+i);
    c.classList.remove('active','done'); l.classList.remove('active');
    if (i<n)       { c.classList.add('done'); c.innerHTML=''; }
    else if (i===n){ c.classList.add('active'); c.innerHTML='<span>'+i+'</span>'; l.classList.add('active'); }
    else           { c.innerHTML='<span>'+i+'</span>'; }
    if (i<4) document.getElementById('ln'+i).classList.toggle('done', i<n);
  });
  cur=n; window.scrollTo({top:0,behavior:'smooth'});
}

// ── Validação ─────────────────────────────────────────────────
function req(id,fid){ const v=document.getElementById(id).value.trim(); document.getElementById(fid).classList.toggle('invalid',!v); return !!v; }
function emailOk(){ const v=document.getElementById('email').value.trim(); const ok=/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(v); document.getElementById('f-email').classList.toggle('invalid',!ok); return ok; }
function cpfOk(){ const v=document.getElementById('cpf').value.replace(/\D/g,''); const ok=v.length===11; document.getElementById('f-cpf').classList.toggle('invalid',!ok); return ok; }
function cepOk(){ const v=document.getElementById('cep').value.replace(/\D/g,''); const ok=v.length===8; document.getElementById('f-cep').classList.toggle('invalid',!ok); return ok; }
function telOk(){ const v=document.getElementById('tel').value.replace(/\D/g,''); const ok=v.length>=10&&v.length<=11; document.getElementById('f-tel').classList.toggle('invalid',!ok); return ok; }

function validate(step){
  if(step===1){let ok=true;const srv=document.querySelector('input[name="servico"]:checked');if(!srv){document.getElementById('f-servico').classList.add('invalid');ok=false;}else{document.getElementById('f-servico').classList.remove('invalid');}if(!req('nome','f-nome'))ok=false;if(!cpfOk())ok=false;if(!req('rg','f-rg'))ok=false;if(!req('nasc','f-nasc'))ok=false;if(!req('form','f-form'))ok=false;if(!req('ecivil','f-ecivil'))ok=false;const pv=document.getElementById('profissao').value.trim();if(!pv){document.getElementById('f-profissao').classList.add('invalid');ok=false;}else{document.getElementById('f-profissao').classList.remove('invalid');}const av=document.getElementById('ativ').value.trim();if(!av){document.getElementById('f-ativ').classList.add('invalid');ok=false;}else{document.getElementById('f-ativ').classList.remove('invalid');}return ok;}
  if(step===2){let ok=true;if(!cepOk())ok=false;if(!req('uf','f-uf'))ok=false;if(!req('cidade','f-cidade'))ok=false;if(!req('bairro','f-bairro'))ok=false;if(!req('rua','f-rua'))ok=false;if(!req('num','f-num'))ok=false;if(!req('comp','f-comp'))ok=false;return ok;}
  if(step===3){let ok=true;if(!telOk())ok=false;if(!emailOk())ok=false;return ok;}
  return true;
}
function goNext(s){if(validate(s))setStep(s+1);}
function goBack(s){setStep(s-1);}

// ── CEP ───────────────────────────────────────────────────────
function buscarCep(){
  const cep=document.getElementById('cep').value.replace(/\D/g,'');
  if(cep.length!==8){document.getElementById('f-cep').classList.add('invalid');return;}
  fetch('https://viacep.com.br/ws/'+cep+'/json/')
    .then(r=>r.json()).then(d=>{
      if(d.erro){document.getElementById('f-cep').classList.add('invalid');return;}
      document.getElementById('rua').value=d.logradouro||'';
      document.getElementById('bairro').value=d.bairro||'';
      document.getElementById('cidade').value=d.localidade||'';
      document.getElementById('uf').value=d.uf||'SP';
      document.getElementById('f-cep').classList.remove('invalid');
    }).catch(()=>document.getElementById('f-cep').classList.add('invalid'));
}

// ── Dados ─────────────────────────────────────────────────────
function v(id){const el=document.getElementById(id);return el?el.value.trim():'';}
function radio(nm){const el=document.querySelector('input[name="'+nm+'"]:checked');return el?el.value:'';}
function getDados(){return{servico:radio('servico'),nome:v('nome'),cpf:v('cpf'),rg:v('rg'),nascimento:v('nasc'),estadoCivil:v('ecivil'),formacao:v('form'),profissao:v('profissao'),atividade:v('ativ'),cep:v('cep'),logradouro:v('rua'),numero:v('num'),complemento:v('comp'),bairro:v('bairro'),cidade:v('cidade'),uf:v('uf'),telefone:v('tel'),email:v('email'),fumante:radio('fum'),pep:radio('pep'),peso:v('peso'),altura:v('altura')};}

// ── Finalizar ─────────────────────────────────────────────────
function finalizar(){
  const btn=document.getElementById('btnFinalizar');
  btn.disabled=true;
  btn.innerHTML='<span class="spinner"></span> Enviando...';
  const dados=getDados();
  const form = new FormData();
  form.append('data', JSON.stringify(dados));
  fetch(SCRIPT_URL, {method:'POST', body: form})
    .then(()=>mostrarSucesso(dados))
    .catch(()=>mostrarSucesso(dados));
}

function mostrarSucesso(dados){
  setStep(5);
  const pares=[['Serviço',dados.servico],['Nome',dados.nome],['CPF',dados.cpf],['Cidade',dados.cidade+(dados.uf?' / '+dados.uf:'')],['Telefone',dados.telefone],['E-mail',dados.email],['Registrado',new Date().toLocaleDateString('pt-BR')]];
  document.getElementById('resumo').innerHTML=pares.map(([k,val])=>`<div class="record-row"><span>${k}</span><span>${val||'—'}</span></div>`).join('');
}

function novoCadastro(){
  document.querySelectorAll('input:not([type=radio])').forEach(i=>i.value='');
  document.querySelectorAll('select').forEach(s=>s.selectedIndex=0);
  document.querySelectorAll('input[type=radio]').forEach(r=>r.checked=false);
  document.querySelectorAll('.invalid').forEach(e=>e.classList.remove('invalid'));
  const btn=document.getElementById('btnFinalizar');
  btn.disabled=false; btn.innerHTML='✓ Finalizar Cadastro';
  setStep(1);
}
</script>
</body>
</html>

