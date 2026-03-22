<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=5.0, user-scalable=no, viewport-fit=cover">
    <meta name="theme-color" content="#0D1929">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <link rel="icon" type="image/jpeg" href="https://i.ibb.co/Fk3ZvkGX/migroop-logo.jpg">
    <link rel="apple-touch-icon" href="https://i.ibb.co/Fk3ZvkGX/migroop-logo.jpg">
    <link rel="manifest" href="manifest.json">
    <title>MIGROPP</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.4.1/papaparse.min.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --bg: #0D1929; --bg-card: #152238; --bg-elevated: #1A2B45; --border: #243554;
            --text: #FFFFFF; --text-secondary: #A8B5C8; --text-muted: #6B7A90;
            --accent: #3B82F6; --success: #10B981; --warning: #F59E0B; --danger: #EF4444; --orange: #F97316;
            --safe-top: env(safe-area-inset-top, 0px);
            --safe-bottom: env(safe-area-inset-bottom, 0px);
        }
        * { box-sizing: border-box; margin: 0; padding: 0; -webkit-tap-highlight-color: transparent; }
        body { font-family: 'Inter', -apple-system, sans-serif; background: var(--bg); color: var(--text); min-height: 100vh; overscroll-behavior: none; }
        
        #splash { position: fixed; inset: 0; z-index: 9999; display: flex; flex-direction: column; align-items: center; justify-content: center; background: var(--bg); transition: opacity 0.5s; padding-top: var(--safe-top); padding-bottom: var(--safe-bottom); }
        #splash.hide { opacity: 0; pointer-events: none; }
        .splash-logo { width: 120px; height: 120px; border-radius: 24px; overflow: hidden; margin-bottom: 1.5rem; box-shadow: 0 10px 40px rgba(59, 130, 246, 0.3); }
        .splash-logo img { width: 100%; height: 100%; object-fit: cover; }
        .splash-title { font-size: 2rem; font-weight: 700; letter-spacing: 0.1em; margin-bottom: 0.25rem; }
        .splash-subtitle { font-size: 0.9rem; color: var(--text-secondary); letter-spacing: 0.15em; margin-bottom: 2rem; text-align: center; }
        .splash-loader { width: 120px; height: 3px; background: var(--bg-elevated); border-radius: 3px; overflow: hidden; }
        .splash-loader-bar { height: 100%; background: linear-gradient(90deg, var(--accent), var(--success)); animation: load 1.5s ease forwards; }
        @keyframes load { to { width: 100%; } }
        
        #app { display: none; min-height: 100vh; }
        #app.visible { display: block; }
        
        .header { background: var(--bg); padding: 1rem 1rem 0; position: sticky; top: 0; z-index: 100; padding-top: calc(1rem + var(--safe-top)); }
        .header-content { display: flex; align-items: center; justify-content: space-between; margin-bottom: 1rem; }
        .header-logo { display: flex; align-items: center; gap: 0.75rem; }
        .header-logo img { width: 44px; height: 44px; border-radius: 10px; object-fit: cover; }
        .header-title { font-size: 1.4rem; font-weight: 700; letter-spacing: 0.05em; line-height: 1.1; }
        .header-btn { width: 40px; height: 40px; background: var(--bg-card); border: 1px solid var(--border); border-radius: 10px; display: flex; align-items: center; justify-content: center; cursor: pointer; }
        .header-btn svg { color: var(--text-secondary); }
        
        .search-box { position: relative; margin-bottom: 0.75rem; }
        .search-input { width: 100%; padding: 0.85rem 2.5rem 0.85rem 2.75rem; background: var(--bg-card); border: 1px solid var(--border); border-radius: 12px; color: var(--text); font-size: 0.95rem; outline: none; }
        .search-clear { position: absolute; right: 0.75rem; top: 50%; transform: translateY(-50%); color: var(--text-muted); font-size: 1.2rem; cursor: pointer; display: none; user-select: none; z-index: 10; }
        
        .stats-bar { display: flex; align-items: center; gap: 1.5rem; padding: 0.5rem 0; border-bottom: 1px solid var(--border); margin-bottom: 0.75rem; font-size: 0.8rem; color: var(--text-secondary); }
        .categories { display: flex; gap: 0.5rem; padding: 0 0 0.75rem; overflow-x: auto; }
        .category-chip { flex-shrink: 0; padding: 0.4rem 0.85rem; background: var(--bg-card); border: 1px solid var(--border); border-radius: 16px; font-size: 0.75rem; color: var(--text-secondary); cursor: pointer; white-space: nowrap; }
        .category-chip.active { background: var(--accent); border-color: var(--accent); color: white; }
        
        .material-list { padding: 0 1rem; padding-bottom: calc(100px + var(--safe-bottom)); }
        .material-card { background: var(--bg-card); border: 1px solid var(--border); border-radius: 14px; padding: 0.85rem; margin-bottom: 0.6rem; cursor: pointer; display: flex; gap: 0.85rem; }
        .material-card:active { transform: scale(0.98); }
        .material-image { width: 60px; height: 60px; border-radius: 10px; overflow: hidden; background: var(--bg-elevated); flex-shrink: 0; }
        .material-image img { width: 100%; height: 100%; object-fit: cover; }
        .material-info { flex: 1; min-width: 0; }
        .material-code { font-size: 0.8rem; font-weight: 600; color: var(--accent); }
        .material-location { font-size: 0.7rem; color: var(--warning); background: rgba(245, 158, 11, 0.1); padding: 0.2rem 0.5rem; border-radius: 6px; }
        .material-name { font-size: 0.9rem; font-weight: 500; line-height: 1.35; display: -webkit-box; -webkit-line-clamp: 2; -webkit-box-orient: vertical; overflow: hidden; margin: 0.35rem 0; }
        .material-category { font-size: 0.65rem; font-weight: 600; padding: 0.25rem 0.6rem; border-radius: 6px; text-transform: uppercase; }
        
        .cat-Estaciones { background: rgba(16, 185, 129, 0.15); color: #10B981; }
        .cat-Reguladores { background: rgba(239, 68, 68, 0.15); color: #EF4444; }
        .cat-Polietileno { background: rgba(59, 130, 246, 0.15); color: #3B82F6; }
        .cat-Acero { background: rgba(139, 92, 246, 0.15); color: #8B5CF6; }
        .cat-Galvanizados { background: rgba(245, 158, 11, 0.15); color: #F59E0B; }
        .cat-Medidores { background: rgba(6, 182, 212, 0.15); color: #06B6D4; }
        .cat-Internas { background: rgba(236, 72, 153, 0.15); color: #EC4899; }
        .cat-Acometidas { background: rgba(20, 184, 166, 0.15); color: #14B8A6; }
        .cat-repherra { background: rgba(107, 122, 144, 0.15); color: #A8B5C8; }
        
        #detail { position: fixed; inset: 0; z-index: 200; background: var(--bg); transform: translateX(100%); transition: transform 0.3s; overflow-y: auto; }
        #detail.open { transform: translateX(0); }
        .detail-header { display: flex; align-items: center; gap: 0.75rem; padding: 1rem; border-bottom: 1px solid var(--border); position: sticky; top: 0; background: var(--bg); z-index: 10; padding-top: calc(1rem + var(--safe-top)); }
        .detail-back { width: 38px; height: 38px; background: var(--bg-card); border: 1px solid var(--border); border-radius: 10px; display: flex; align-items: center; justify-content: center; cursor: pointer; }
        .detail-image-container { aspect-ratio: 4/3; background: var(--bg-card); display: flex; align-items: center; justify-content: center; cursor: pointer; }
        .detail-image { max-width: 100%; max-height: 100%; object-fit: contain; }
        .detail-content { padding: 1.5rem 1rem; display: flex; flex-direction: column; gap: 1.5rem; padding-bottom: calc(2rem + var(--safe-bottom)); }
        .detail-row-header { display: flex; justify-content: space-between; width: 100%; gap: 1rem; }
        .tag-group { display: flex; flex-direction: column; gap: 0.4rem; }
        .tag-group.right { align-items: flex-end; text-align: right; }
        .tag-label { font-size: 0.7rem; font-weight: 700; color: var(--text-muted); text-transform: uppercase; }
        .tag-code { font-size: 1.1rem; font-weight: 700; font-family: monospace; color: var(--accent); background: rgba(59, 130, 246, 0.1); padding: 0.6rem 1rem; border-radius: 8px; border: 1px solid rgba(59, 130, 246, 0.3); display: inline-block; }
        .tag-desc { font-size: 1rem; line-height: 1.6; color: var(--text); white-space: pre-wrap; background: var(--bg-elevated); padding: 1rem; border-radius: 8px; border-left: 4px solid var(--accent); }
        .tag-location { font-size: 1.2rem; font-weight: 700; color: white; background: linear-gradient(135deg, var(--warning), var(--orange)); padding: 1rem; border-radius: 10px; text-align: center; }
        .tag-category { display: inline-block; padding: 0.5rem 1rem; border-radius: 8px; font-weight: 600; font-size: 0.85rem; text-transform: uppercase; }
        .detail-back-btn { width: 100%; padding: 0.9rem; background: var(--bg-card); border: 1px solid var(--border); border-radius: 12px; color: var(--text-secondary); font-weight: 600; cursor: pointer; margin-top: 1rem; }
        
        #admin { position: fixed; inset: 0; z-index: 200; background: var(--bg); transform: translateY(100%); transition: transform 0.3s; overflow-y: auto; }
        #admin.open { transform: translateY(0); }
        .admin-content { padding: 5rem 1rem 2rem; padding-top: calc(5rem + var(--safe-top)); padding-bottom: calc(2rem + var(--safe-bottom)); }
        .admin-tabs { display: flex; gap: 0.5rem; margin-bottom: 1.5rem; border-bottom: 1px solid var(--border); padding-bottom: 0.75rem; flex-wrap: wrap; }
        .admin-tab { padding: 0.5rem 1rem; background: transparent; border: none; border-radius: 8px; color: var(--text-secondary); font-size: 0.85rem; cursor: pointer; }
        .admin-tab.active { background: var(--accent); color: white; }
        .admin-title { font-size: 1.3rem; font-weight: 700; margin-bottom: 1.5rem; }
        .admin-form { background: var(--bg-card); border: 1px solid var(--border); border-radius: 14px; padding: 1.25rem; margin-bottom: 1rem; }
        .form-row { margin-bottom: 1rem; }
        .form-label { display: block; font-size: 0.75rem; font-weight: 600; color: var(--text-muted); text-transform: uppercase; margin-bottom: 0.4rem; }
        .form-input, .form-select { width: 100%; padding: 0.75rem 1rem; background: var(--bg); border: 1px solid var(--border); border-radius: 10px; color: var(--text); font-size: 0.9rem; outline: none; }
        .btn { padding: 0.75rem 1.5rem; border: none; border-radius: 10px; font-size: 0.9rem; font-weight: 600; cursor: pointer; width: 100%; }
        .btn-success { background: var(--success); color: white; }
        .btn-secondary { background: var(--bg-elevated); color: var(--text); border: 1px solid var(--border); }
        .btn-primary { background: var(--accent); color: white; }
        
        .import-zone { border: 2px dashed var(--border); border-radius: 14px; padding: 2rem 1.5rem; text-align: center; cursor: pointer; }
        .admin-table-container { background: var(--bg-card); border-radius: 14px; overflow: hidden; }
        .admin-table { width: 100%; border-collapse: collapse; }
        .admin-table th, .admin-table td { padding: 0.75rem 1rem; text-align: left; border-bottom: 1px solid var(--border); }
        .admin-table th { font-size: 0.7rem; color: var(--text-muted); text-transform: uppercase; background: var(--bg-elevated); }
        .table-actions { display: flex; gap: 0.5rem; }
        .table-btn { width: 32px; height: 32px; border: none; border-radius: 8px; cursor: pointer; }
        .table-btn-edit { background: rgba(59, 130, 246, 0.15); color: var(--accent); }
        .table-btn-delete { background: rgba(239, 68, 68, 0.15); color: var(--danger); }
        
        .pagination { display: flex; align-items: center; justify-content: center; gap: 0.5rem; padding: 1rem; }
        .pagination-btn { width: 36px; height: 36px; background: var(--bg); border: 1px solid var(--border); border-radius: 8px; color: var(--text-secondary); cursor: pointer; }
        .pagination-btn.active { background: var(--accent); color: white; }
        
        .modal-overlay { position: fixed; inset: 0; background: rgba(0, 0, 0, 0.7); z-index: 400; display: flex; align-items: center; justify-content: center; padding: 1rem; opacity: 0; visibility: hidden; transition: all 0.25s; }
        .modal-overlay.show { opacity: 1; visibility: visible; }
        .modal { background: var(--bg-card); border-radius: 16px; width: 100%; max-width: 450px; max-height: 90vh; overflow-y: auto; }
        .modal-header { display: flex; align-items: center; justify-content: space-between; padding: 1rem 1.25rem; border-bottom: 1px solid var(--border); }
        .modal-body { padding: 1.25rem; }
        .modal-footer { display: flex; gap: 0.75rem; padding: 1rem 1.25rem; border-top: 1px solid var(--border); }
        
        /* FULLSCREEN ZOOM */
        #fullscreen { position: fixed; inset: 0; z-index: 300; background: rgba(0, 0, 0, 0.95); display: flex; align-items: center; justify-content: center; opacity: 0; visibility: hidden; transition: all 0.25s; overflow: hidden; }
        #fullscreen.show { opacity: 1; visibility: visible; }
        .fs-close { position: absolute; top: 1rem; right: 1rem; width: 42px; height: 42px; background: rgba(255,255,255,0.1); border: none; border-radius: 50%; color: white; cursor: pointer; z-index: 10; }
        .fs-container { width: 100%; height: 100%; display: flex; align-items: center; justify-content: center; touch-action: none; }
        .fs-img { max-width: 100%; max-height: 100%; object-fit: contain; cursor: grab; user-select: none; -webkit-user-drag: none; }
        .fs-code-overlay { position: absolute; bottom: 0; left: 0; right: 0; padding: 2rem 1rem 1.5rem; background: linear-gradient(transparent, rgba(0,0,0,0.9)); display: flex; justify-content: center; pointer-events: none; padding-bottom: calc(1.5rem + var(--safe-bottom)); }
        .fs-code-box { background: rgba(255,255,255,.15); backdrop-filter: blur(10px); padding: 0.6rem 1.5rem; border-radius: 50px; color: white; font-weight: 600; }
        
        .toast { position: fixed; bottom: 90px; left: 50%; transform: translateX(-50%) translateY(20px); padding: 0.85rem 1.25rem; background: var(--bg-card); border-radius: 10px; font-size: 0.9rem; opacity: 0; transition: all 0.25s; z-index: 500; text-align: center; max-width: 90vw; }
        .toast.success { border: 1px solid var(--success); }
        .toast.error { border: 1px solid var(--danger); }
        .toast.show { opacity: 1; transform: translateX(-50%) translateY(0); }
        .hidden { display: none; }
    </style>
</head>
<body>
    <div id="splash">
        <div class="splash-logo"><img src="https://i.ibb.co/Fk3ZvkGX/migroop-logo.jpg" alt="MIGROPP"></div>
        <h1 class="splash-title">MIGROPP</h1>
        <p class="splash-subtitle">Gestion de Almacen</p>
        <div class="splash-loader"><div class="splash-loader-bar"></div></div>
    </div>
    
    <div id="app">
        <header class="header">
            <div class="header-content">
                <div class="header-logo"><img src="https://i.ibb.co/Fk3ZvkGX/migroop-logo.jpg"><span class="header-title">MIGROPP</span></div>
                <button class="header-btn" id="fabBtn"><svg width="18" height="18" fill="none" stroke="currentColor" stroke-width="2"><circle cx="9" cy="9" r="2"/><path d="M9.2 1h-.4a1.8 1.8 0 0 0-1.8 1.8v.1a1.8 1.8 0 0 1-1 1.6l-.4.2a1.8 1.8 0 0 1-1.8 0l-.1-.1a1.8 1.8 0 0 0-2.5.7l-.2.3a1.8 1.8 0 0 0 .7 2.5l.1.1a1.8 1.8 0 0 1 .9 1.5v.5a1.8 1.8 0 0 1-.9 1.5l-.1.1a1.8 1.8 0 0 0-.7 2.5l.2.3a1.8 1.8 0 0 0 2.5.7l.1-.1a1.8 1.8 0 0 1 1.8 0l.4.2a1.8 1.8 0 0 1 1 1.6v.1A1.8 1.8 0 0 0 8.8 19h.4a1.8 1.8 0 0 0 1.8-1.8v-.1a1.8 1.8 0 0 1 1-1.6l.4-.2a1.8 1.8 0 0 1 1.8 0l.1.1a1.8 1.8 0 0 0 2.5-.7l.2-.3a1.8 1.8 0 0 0-.7-2.5l-.1-.1a1.8 1.8 0 0 1-.9-1.5v-.5a1.8 1.8 0 0 1 .9-1.5l.1-.1a1.8 1.8 0 0 0 .7-2.5l-.2-.3a1.8 1.8 0 0 0-2.5-.7l-.1.1a1.8 1.8 0 0 1-1.8 0l-.4-.2a1.8 1.8 0 0 1-1-1.6v-.1A1.8 1.8 0 0 0 9.2 1z"/></svg></button>
            </div>
            <div class="search-box"><input type="text" class="search-input" id="searchInput" placeholder="Buscar..."><span class="search-clear" id="clearSearch">✕</span></div>
            <div class="stats-bar"><div class="stat-item"><strong id="totalCount">0</strong> materiales</div><div class="stat-item"><strong id="filteredCount">0</strong> resultados</div></div>
            <div class="categories" id="categories"></div>
        </header>
        <main class="material-list" id="materialList"></main>
    </div>
    
    <div id="detail">
        <div class="detail-header"><button class="detail-back" id="detailBack">←</button><span style="font-weight:600">Detalle</span></div>
        <div class="detail-image-container" id="detailImageContainer"><img class="detail-image" id="detailImage"></div>
        <div class="detail-content">
            <div class="detail-row-header">
                <div class="tag-group"><span class="tag-label">Código</span><span id="detailCode" class="tag-code"></span></div>
                <div class="tag-group right"><span class="tag-label">Categoría</span><span id="detailCat" class="tag-category material-category"></span></div>
            </div>
            <div><span class="tag-label">Descripción</span><div id="detailDesc" class="tag-desc"></div></div>
            <div><span class="tag-label">Ubicación</span><div id="detailLoc" class="tag-location"></div></div>
            <button class="detail-back-btn" id="detailBackBtn">Volver</button>
        </div>
    </div>
    
    <div id="fullscreen"><button class="fs-close" id="fsClose">X</button><div class="fs-container" id="fsContainer"><img class="fs-img" id="fsImg"></div><div class="fs-code-overlay"><div class="fs-code-box" id="fsCodeOverlay"></div></div></div>
    
    <div id="admin">
        <div class="detail-header"><button class="detail-back" id="adminBack">←</button><span style="font-weight:600">Admin</span></div>
        <div class="admin-content">
            <div class="admin-tabs"><button class="admin-tab active" data-tab="tabList">Lista</button><button class="admin-tab" data-tab="tabImport">Importar</button><button class="admin-tab" data-tab="tabConfig">Config</button></div>
            <div id="tabList"><div class="admin-title">Materiales</div><div class="admin-table-container"><table class="admin-table"><thead><tr><th>Código</th><th>Descripción</th><th>Ubicación</th><th>Acciones</th></tr></thead><tbody id="adminTableBody"></tbody></table><div class="pagination" id="pagination"></div></div></div>
            <div id="tabImport" class="hidden"><div class="admin-title">Importar CSV</div><p style="color:var(--text-secondary);margin-bottom:1rem;font-size:0.85rem">Formato: Categoría;Código;Descripción;Ubicación;Imagen</p><div class="import-zone" id="importZone"><svg width="40" height="40" fill="none" stroke="currentColor" stroke-width="1.5"><path d="M20 16v4a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2v-4M16 8l-6-6-6 6M10 2v12"/></svg><p>Arrastra CSV</p></div><input type="file" id="fileInput" accept=".csv" style="display:none"></div>
            <div id="tabConfig" class="hidden"><div class="admin-title">Config</div>
                <div class="admin-form" style="border-color:var(--success)"><h3 style="font-size:0.9rem;color:var(--success)">App Instalada</h3><p style="font-size:0.8rem;color:var(--text-secondary);margin-bottom:1rem">Datos permanentes. Navegación con botón atrás.</p><button class="btn btn-success" id="exportApp">Descargar Versión Compartible</button></div>
                <div class="admin-form"><h3 style="margin-bottom:1rem">Seguridad</h3><div class="form-row"><label class="form-label">Contraseña Actual</label><input type="password" class="form-input" id="configCurrentPass"></div><div class="form-row"><label class="form-label">Nueva Contraseña</label><input type="password" class="form-input" id="configNewPass"></div><button class="btn btn-primary" id="changePassBtn">Cambiar</button><button class="btn btn-secondary" id="resetPassBtn" style="margin-top:0.5rem">Restablecer</button></div>
            </div>
        </div>
    </div>
    
    <div class="modal-overlay" id="loginModal"><div class="modal" style="max-width:350px;text-align:center"><div style="padding:2rem 1.5rem"><h3>Acceso</h3><br><input type="password" id="passwordInput" class="form-input" placeholder="Contraseña" style="text-align:center"></div><div style="display:flex;border-top:1px solid var(--border)"><button class="btn btn-secondary" id="loginCancel" style="flex:1;border-radius:0 0 0 16px;border:none">Cancelar</button><button class="btn btn-primary" id="loginSubmit" style="flex:1;border-radius:0 0 16px 0;border:none">Entrar</button></div></div></div>
    <div class="modal-overlay" id="editModal"><div class="modal"><div class="modal-header"><span>Editar</span><button class="modal-close" id="editClose">X</button></div><div class="modal-body"><div class="form-row"><label class="form-label">Código</label><input class="form-input" id="editCodigo"></div><div class="form-row"><label class="form-label">Descripción</label><input class="form-input" id="editDescripcion"></div><div class="form-row"><label class="form-label">Categoría</label><select class="form-select" id="editCategoria"><option>Estaciones</option><option>Reguladores</option><option>Polietileno</option><option>Acero</option><option>Galvanizados</option><option>Medidores</option><option>Internas</option><option>Acometidas</option><option>Rep.Herra.Varios</option></select></div><div class="form-row"><label class="form-label">Ubicación</label><input class="form-input" id="editUbicacion"></div><div class="form-row"><label class="form-label">Imagen</label><input class="form-input" id="editImagen"></div></div><div class="modal-footer"><button class="btn btn-secondary" id="editCancel">Cancelar</button><button class="btn btn-danger" id="editDelete">Eliminar</button><button class="btn btn-success" id="editSave">Guardar</button></div></div></div>
    <div class="modal-overlay" id="deleteModal"><div class="modal" style="max-width:350px"><div class="modal-header"><span>Eliminar</span></div><div class="modal-body"><p style="text-align:center">¿Eliminar?</p><p id="deleteInfo" style="text-align:center;color:var(--accent);margin-top:.5rem"></p></div><div class="modal-footer" style="justify-content:center"><button class="btn btn-secondary" id="deleteCancel">No</button><button class="btn btn-danger" id="deleteConfirm">Si</button></div></div></div>
    
    <div class="toast" id="toast"></div>

<script>
const $=id=>document.getElementById(id);
let materials=[],filtered=[],cat='Todos',editIdx=-1,page=1;
const itemsPerPage=20;
let searchTimeout=null;
let lastBackPress=0; 

const DEFAULT_PASSWORD = "migropp";
function getPassword(){ try { return localStorage.getItem('migropp_pass') || DEFAULT_PASSWORD; } catch(e) { return DEFAULT_PASSWORD; } }
function setPassword(p){ try { localStorage.setItem('migropp_pass', p); } catch(e) {} }
const DEFAULT_DATA = []; 
const getClass=c=>({Estaciones:'cat-Estaciones',Reguladores:'cat-Reguladores',Polietileno:'cat-Polietileno',Acero:'cat-Acero',Galvanizados:'cat-Galvanizados',Medidores:'cat-Medidores',Internas:'cat-Internas',Acometidas:'cat-Acometidas'})[c]||'cat-repherra';

function init(){ 
    let storedData = null;
    try { const s = localStorage.getItem('migropp'); if(s) storedData = JSON.parse(s); } catch(e) { storedData = null; }
    if(storedData && storedData.length > 0) { materials = storedData; }
    else if (DEFAULT_DATA && DEFAULT_DATA.length > 0) { materials = DEFAULT_DATA; save(); }
    else { materials = []; }
    setTimeout(()=>{$('splash').classList.add('hide');$('app').classList.add('visible');renderCats();render();initZoom();}, 1500); 
}
function save(){ try { localStorage.setItem('migropp', JSON.stringify(materials)); } catch(e) { console.error("Error guardando", e); } }

// Control Nativo (Botón Atrás)
function handlePopState(e){
    if($('editModal').classList.contains('show')) { $('editModal').classList.remove('show'); return; }
    if($('deleteModal').classList.contains('show')) { $('deleteModal').classList.remove('show'); return; }
    if($('admin').classList.contains('open')) { $('admin').classList.remove('open'); return; }
    if($('detail').classList.contains('open')) { $('detail').classList.remove('open'); return; }
    if($('fullscreen').classList.contains('show')) { $('fullscreen').classList.remove('show'); resetFsZoom(); return; }

    const now = Date.now();
    if (now - lastBackPress < 2500) { return; } 
    else {
        lastBackPress = now;
        toast('Presione de nuevo para salir', 0);
        history.pushState({main: true}, ''); 
    }
}
window.addEventListener('popstate', handlePopState);
history.pushState({main: true}, ''); 
function pushState(){ history.pushState({modal: true}, ''); }

function openDetail(m){
    $('detailImage').src=m.imagen||''; 
    $('detailCode').textContent=m.codigo; 
    $('detailDesc').textContent=m.descripcion; 
    $('detailCat').className = 'tag-category material-category ' + getClass(m.categoria);
    $('detailCat').textContent=m.categoria;
    $('detailLoc').textContent=m.ubicacion;
    $('detail').classList.add('open'); pushState(); 
}
function closeDetail(){ history.back(); }
 $('detailBackBtn').onclick = closeDetail; $('detailBack').onclick=closeDetail; 

let touchStartX=0,touchCurrentX=0,isSwiping=false;
 $('detail').addEventListener('touchstart',e=>{touchStartX=e.touches[0].clientX;isSwiping=true;$('detail').classList.add('dragging');},{passive:true});
 $('detail').addEventListener('touchmove',e=>{if(!isSwiping)return;touchCurrentX=e.touches[0].clientX;const diff=touchCurrentX-touchStartX;if(diff>0)$('detail').style.transform='translateX('+diff+'px)';},{passive:true});
 $('detail').addEventListener('touchend',e=>{isSwiping=false;$('detail').classList.remove('dragging');const diff=touchCurrentX-touchStartX;if(diff>100)closeDetail();else $('detail').style.transform='';touchStartX=0;touchCurrentX=0;},{passive:true});

function renderCats(){ const c=['Todos',...new Set(materials.map(m=>m.categoria))]; $('categories').innerHTML=c.map(x=>'<span class="category-chip'+(x===cat?' active':'')+'">'+x+'</span>').join(''); $('categories').querySelectorAll('.category-chip').forEach((el,i)=>el.onclick=()=>{cat=c[i];renderCats();filter()}); }
function filter(){ const q=$('searchInput').value.toLowerCase(); filtered=materials.filter(m=>(cat==='Todos'||m.categoria===cat)&&(m.codigo.toLowerCase().includes(q)||m.descripcion.toLowerCase().includes(q))); $('filteredCount').textContent=filtered.length; render(); }
function render(){
    $('totalCount').textContent=materials.length;
    if(materials.length === 0){ $('materialList').innerHTML='<p style="text-align:center;color:var(--text-muted);padding:3rem">Sin datos.<br>Importa CSV.</p>'; return; }
    if(filtered.length === 0){ $('materialList').innerHTML='<p style="text-align:center;color:var(--text-muted);padding:3rem">No encontrado</p>'; return; }
    $('materialList').innerHTML=filtered.map((m,i)=>'<div class="material-card" data-i="'+i+'"><div class="material-image">'+(m.imagen?'<img src="'+m.imagen+'" loading="lazy">':'')+'</div><div class="material-info"><div style="display:flex;justify-content:space-between"><span class="material-code">'+m.codigo+'</span><span class="material-location">'+m.ubicacion+'</span></div><div class="material-name">'+m.descripcion+'</div><span class="material-category '+getClass(m.categoria)+'">'+m.categoria+'</span></div></div>').join('');
}
 $('materialList').onclick=(e)=>{const card=e.target.closest('.material-card');if(!card)return;openDetail(filtered[+card.dataset.i]);};
 $('clearSearch').onclick = () => { $('searchInput').value = ''; filter(); $('clearSearch').style.display = 'none'; $('searchInput').focus(); };

// ZOOM RESTAURADO (Permite mover la imagen)
let fsScale=1,fsX=0,fsY=0,sd=0,ss=1,sx=0,sy=0,drg=false,pn=false;
function uT(){$('fsImg').style.transform='translate('+fsX+'px, '+fsY+'px) scale('+fsScale+')';}
function rZ(){fsScale=1;fsX=0;fsY=0;uT();}
function initZoom(){
    $('detailImageContainer').addEventListener('click',()=>{if(!$('detailImage').src)return;rZ();$('fsImg').src=$('detailImage').src;$('fsCodeOverlay').textContent=$('detailCode').textContent;$('fullscreen').classList.add('show');pushState();});
    $('fsImg').ondblclick=()=>{if(fsScale>1)rZ();else{fsScale=2.5;uT();}};
    $('fsContainer').ontouchstart=e=>{if(e.touches.length===2){pn=true;sd=Math.hypot(e.touches[0].clientX-e.touches[1].clientX,e.touches[0].clientY-e.touches[1].clientY);ss=fsScale;}else if(e.touches.length===1&&fsScale>1){drg=true;sx=e.touches[0].clientX-fsX;sy=e.touches[0].clientY-fsY;}};
    $('fsContainer').ontouchmove=e=>{e.preventDefault();if(pn&&e.touches.length===2){const d=Math.hypot(e.touches[0].clientX-e.touches[1].clientX,e.touches[0].clientY-e.touches[1].clientY);fsScale=Math.max(1,Math.min(5,ss*(d/sd)));uT();}else if(drg&&e.touches.length===1){fsX=e.touches[0].clientX-sx;fsY=e.touches[0].clientY-sy;uT();}};
    $('fsContainer').ontouchend=()=>{drg=false;pn=false;};
    $('fsContainer').onwheel=e=>{e.preventDefault();fsScale=Math.max(1,Math.min(5,fsScale*(e.deltaY>0?.9:1.1)));if(fsScale===1){fsX=0;fsY=0;}uT();};
    $('fsImg').onmousedown=e=>{if(fsScale>1){drg=true;sx=e.clientX-fsX;sy=e.clientY-fsY;$('fsImg').style.cursor='grabbing';}};
    window.addEventListener('mousemove',e=>{if(drg){fsX=e.clientX-sx;fsY=e.clientY-sy;uT();}});
    window.addEventListener('mouseup',()=>{drg=false;if($('fsImg'))$('fsImg').style.cursor='grab';});
    $('fsClose').addEventListener('click',()=>{$('fullscreen').classList.remove('show');rZ();history.back();});
}

// Admin
 $('fabBtn').onclick=()=>{$('loginModal').classList.add('show');pushState();};
 $('loginCancel').onclick=()=>{$('loginModal').classList.remove('show');history.back();};
 $('loginSubmit').onclick=()=>{if($('passwordInput').value===getPassword()){$('loginModal').classList.remove('show');$('passwordInput').value='';$('admin').classList.add('open');pushState();renderAdmin();}else{toast('Incorrecta',0);}};
 $('passwordInput').onkeydown=(e)=>{if(e.key==='Enter')$('loginSubmit').click();};
function closeAdmin(){$('admin').classList.remove('open');history.back();}
 $('adminBack').onclick=closeAdmin;
 $('admin').onclick=(e)=>{const tab=e.target.closest('.admin-tab');if(!tab)return;document.querySelectorAll('.admin-tab').forEach(t=>t.classList.remove('active'));tab.classList.add('active');$('tabList').classList.add('hidden');$('tabImport').classList.add('hidden');$('tabConfig').classList.add('hidden');const tabId=tab.dataset.tab;if($(tabId))$(tabId).classList.remove('hidden');if(tabId==='tabList')renderAdmin();};

 $('changePassBtn').onclick=()=>{const curr=$('configCurrentPass').value,newP=$('configNewPass').value;if(curr!==getPassword()){toast('Actual incorrecta',0);return;}if(newP.length<4){toast('Min 4',0);return;}setPassword(newP);toast('Cambiada');};
 $('resetPassBtn').onclick=()=>{setPassword(DEFAULT_PASSWORD);toast('Restablecida');};

function renderAdmin(){const start=(page-1)*itemsPerPage,end=start+itemsPerPage,data=materials.slice(start,end);$('adminTableBody').innerHTML=data.map((m,i)=>'<tr><td style="color:var(--accent)">'+m.codigo+'</td><td>'+m.descripcion+'</td><td>'+m.ubicacion+'</td><td><div class="table-actions"><button class="table-btn table-btn-edit" data-i="'+(start+i)+'">E</button><button class="table-btn table-btn-delete" data-i="'+(start+i)+'">X</button></div></td></tr>').join('');const pages=Math.ceil(materials.length/itemsPerPage);$('pagination').innerHTML='<button class="pagination-btn" '+(page===1?'disabled':'')+' onclick="page--;renderAdmin()">&lt;</button>'+[page-1,page,page+1].filter(p=>p>0&&p<=pages).map(p=>'<button class="pagination-btn'+(p===page?' active':'')+'" onclick="page='+p+';renderAdmin()">'+p+'</button>').join('')+'<button class="pagination-btn" '+(page===pages?'disabled':'')+' onclick="page++;renderAdmin()">&gt;</button>';$('adminTableBody').querySelectorAll('.table-btn-edit').forEach(b=>b.onclick=()=>{editIdx=+b.dataset.i;const m=materials[editIdx];$('editCodigo').value=m.codigo;$('editDescripcion').value=m.descripcion;$('editCategoria').value=m.categoria;$('editUbicacion').value=m.ubicacion;$('editImagen').value=m.imagen||'';$('editModal').classList.add('show');pushState();});$('adminTableBody').querySelectorAll('.table-btn-delete').forEach(b=>b.onclick=()=>{editIdx=+b.dataset.i;$('deleteInfo').textContent=materials[editIdx].codigo;$('deleteModal').classList.add('show');pushState();});}
 $('editCancel').onclick=()=>{$('editModal').classList.remove('show');history.back();};
 $('editSave').onclick=()=>{const m=materials[editIdx];m.codigo=$('editCodigo').value.trim();m.descripcion=$('editDescripcion').value.trim();m.categoria=$('editCategoria').value;m.ubicacion=$('editUbicacion').value.trim();m.imagen=$('editImagen').value.trim();save();filter();$('editModal').classList.remove('show');history.back();renderAdmin();toast('Guardado');};
 $('editDelete').onclick=()=>{$('editModal').classList.remove('show');$('deleteInfo').textContent=materials[editIdx].codigo;$('deleteModal').classList.add('show');};
 $('deleteCancel').onclick=()=>{$('deleteModal').classList.remove('show');history.back();};
 $('deleteConfirm').onclick=()=>{materials.splice(editIdx,1);save();renderCats();filter();$('deleteModal').classList.remove('show');history.back();renderAdmin();toast('Eliminado');};

// CSV
 $('importZone').onclick=()=>$('fileInput').click();
 $('importZone').ondragover=e=>{e.preventDefault();$('importZone').style.borderColor='var(--accent)'};
 $('importZone').ondragleave=()=>$('importZone').style.borderColor='';
 $('importZone').ondrop=e=>{e.preventDefault();$('importZone').style.borderColor='';if(e.dataTransfer.files[0])process(e.dataTransfer.files[0])};
 $('fileInput').onchange=e=>{if(e.target.files[0])process(e.target.files[0])};

function process(f){
    if(typeof Papa==='undefined'){toast('Error lib',0);return;}
    Papa.parse(f,{header:false,skipEmptyLines:true,complete:function(results){
        try{
            const rows=results.data;if(rows.length===0){toast('Vacio',0);return;}
            let temp=[];
            rows.forEach(row=>{
                const len=row.length;if(len<4)return;
                let code=String(row[1]||'').trim();if(!/^\d+$/.test(code))return;
                let c=String(row[0]||'').trim();
                let img=String(row[len-1]||'').trim();
                let loc=String(row[len-2]||'').trim();
                let desc="";
                if(len>=5){desc=row.slice(2,len-2).join(';');}else if(len===4){desc=String(row[2]||'').trim();}
                desc=desc.trim();
                temp.push({categoria:c,codigo:code,descripcion:desc,ubicacion:loc,imagen:img});
            });
            if(temp.length>0){materials=temp;save();cat='Todos';$('searchInput').value='';$('clearSearch').style.display='none';renderCats();filter();closeAdmin();toast(temp.length+' importados');}else{toast('Sin datos',0);}
        }catch(err){toast('Error',0);}
    }});
}

// EXPORTAR
 $('exportApp').onclick = () => {
    if(materials.length === 0) { toast("No hay datos", 0); return; }
    const jsonStr = JSON.stringify(materials);
    const base64Data = btoa(unescape(encodeURIComponent(jsonStr)));
    const html = `<!DOCTYPE html><html><head><meta charset="UTF-8"><meta name="viewport" content="width=device-width,initial-scale=1"><title>MIGROPP</title><link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet"><style>:root{--bg:#0D1929;--bg-card:#152238;--bg-elevated:#1A2B45;--border:#243554;--text:#FFF;--text-secondary:#A8B5C8;--accent:#3B82F6;--warning:#F59E0B}body{font-family:Inter,sans-serif;background:var(--bg);color:var(--text);margin:0}#splash{position:fixed;inset:0;display:flex;flex-direction:column;align-items:center;justify-content:center;background:var(--bg);z-index:99;transition:opacity .5s}.hide{opacity:0;pointer-events:none}.splash-logo{width:100px;height:100px;border-radius:20px;overflow:hidden;margin-bottom:1rem;box-shadow:0 10px 30px rgba(59,130,246,.3)}.splash-title{font-size:1.8rem;font-weight:700;letter-spacing:.1em}.splash-subtitle{font-size:.8rem;color:var(--text-secondary);letter-spacing:.1em;margin-bottom:1.5rem}.splash-loader{width:100px;height:2px;background:var(--bg-elevated);border-radius:2px;overflow:hidden}.splash-loader-bar{height:100%;background:linear-gradient(90deg,var(--accent),#10B981);animation:load 1.5s ease forwards}@keyframes load{to{width:100%}}#app{display:none;min-height:100vh}.visible{display:block}.header{background:var(--bg);padding:1rem 1rem 0;position:sticky;top:0;z-index:10}.header-content{display:flex;align-items:center;justify-content:space-between;margin-bottom:1rem}.header-logo{display:flex;align-items:center;gap:.75rem}.header-title{font-size:1.2rem;font-weight:700}.search-box{position:relative;margin-bottom:.75rem}.search-input{width:100%;padding:.8rem 2.2rem .8rem 2.5rem;background:var(--bg-card);border:1px solid var(--border);border-radius:10px;color:var(--text);font-size:.9rem;outline:none}.search-clear{position:absolute;right:.7rem;top:50%;transform:translateY(-50%);color:var(--text-muted);font-size:1.1rem;cursor:pointer;display:none}.stats-bar{display:flex;gap:1rem;padding:.5rem 0;border-bottom:1px solid var(--border);margin-bottom:.7rem;font-size:.75rem;color:var(--text-secondary)}.categories{display:flex;gap:.4rem;padding:0 0 .7rem;overflow-x:auto}.category-chip{padding:.3rem .7rem;background:var(--bg-card);border:1px solid var(--border);border-radius:14px;font-size:.7rem;color:var(--text-secondary);cursor:pointer;white-space:nowrap}.active{background:var(--accent);border-color:var(--accent);color:#fff}.material-list{padding:0 1rem 80px}.material-card{background:var(--bg-card);border:1px solid var(--border);border-radius:12px;padding:.8rem;margin-bottom:.5rem;display:flex;gap:.8rem}.material-image{width:55px;height:55px;border-radius:8px;overflow:hidden;background:var(--bg-elevated);flex-shrink:0}.material-image img{width:100%;height:100%;object-fit:cover}.material-info{flex:1;min-width:0}.material-code{font-size:.75rem;font-weight:600;color:var(--accent)}.material-location{font-size:.65rem;color:var(--warning);background:rgba(245,158,11,.1);padding:.15rem .4rem;border-radius:5px}.material-name{font-size:.85rem;font-weight:500;line-height:1.3;display:-webkit-box;-webkit-line-clamp:2;-webkit-box-orient:vertical;overflow:hidden;margin:.3rem 0}.material-category{font-size:.6rem;font-weight:600;padding:.2rem .5rem;border-radius:5px;text-transform:uppercase;display:inline-block}.cat-Estaciones{background:rgba(16,185,129,.15);color:#10B981}.cat-Reguladores{background:rgba(239,68,68,.15);color:#EF4444}.cat-Polietileno{background:rgba(59,130,246,.15);color:#3B82F6}.cat-Acero{background:rgba(139,92,246,.15);color:#8B5CF6}.cat-Galvanizados{background:rgba(245,158,11,.15);color:#F59E0B}.cat-Medidores{background:rgba(6,182,212,.15);color:#06B6D4}.cat-Internas{background:rgba(236,72,153,.15);color:#EC4899}.cat-Acometidas{background:rgba(20,184,166,.15);color:#14B8A6}#detail{position:fixed;inset:0;z-index:20;background:var(--bg);transform:translateX(100%);transition:transform .3s;overflow-y:auto}.open{transform:translateX(0)}.detail-header{display:flex;align-items:center;gap:.7rem;padding:.8rem;border-bottom:1px solid var(--border);position:sticky;top:0;background:var(--bg);z-index:5}.detail-back{width:35px;height:35px;background:var(--bg-card);border:1px solid var(--border);border-radius:8px;display:flex;align-items:center;justify-content:center;cursor:pointer}.detail-image-container{aspect-ratio:4/3;background:var(--bg-card);display:flex;align-items:center;justify-content:center;cursor:pointer}.detail-image{max-width:100%;max-height:100%;object-fit:contain}.detail-content{padding:1.2rem 1rem;display:flex;flex-direction:column;gap:1.2rem}.detail-row-header{display:flex;justify-content:space-between;width:100%;gap:1rem}.tag-group{display:flex;flex-direction:column;gap:.3rem}.right{align-items:flex-end;text-align:right}.tag-label{font-size:.65rem;font-weight:700;color:var(--text-secondary);text-transform:uppercase}.tag-code{font-size:1rem;font-weight:700;font-family:monospace;color:var(--accent);background:rgba(59,130,246,.1);padding:.5rem .8rem;border-radius:6px;border:1px solid rgba(59,130,246,.2);display:inline-block}.tag-desc{font-size:.9rem;line-height:1.5;color:var(--text);white-space:pre-wrap;background:var(--bg-elevated);padding:.8rem;border-radius:6px;border-left:3px solid var(--accent)}.tag-location{font-size:1.1rem;font-weight:700;color:#fff;background:linear-gradient(135deg,var(--warning),#F97316);padding:.8rem;border-radius:8px;text-align:center}.tag-category{display:inline-block;padding:.4rem .8rem;border-radius:6px;font-weight:600;font-size:.8rem;text-transform:uppercase}.detail-back-btn{width:100%;padding:.8rem;background:var(--bg-card);border:1px solid var(--border);border-radius:10px;color:var(--text-secondary);font-weight:600;cursor:pointer;margin-top:1rem}#fullscreen{position:fixed;inset:0;z-index:30;background:rgba(0,0,0,.9);display:flex;align-items:center;justify-content:center;opacity:0;visibility:hidden}.show{opacity:1;visibility:visible}.fs-close{position:absolute;top:.8rem;right:.8rem;width:38px;height:38px;background:rgba(255,255,255,.1);border:none;border-radius:50%;color:#fff;cursor:pointer;z-index:5}.fs-container{width:100%;height:100%;display:flex;align-items:center;justify-content:center}.fs-img{max-width:100%;max-height:100%;object-fit:contain;cursor:grab}.fs-code-overlay{position:absolute;bottom:0;left:0;right:0;padding:1.5rem 1rem 1rem;background:linear-gradient(transparent,rgba(0,0,0,.8));display:flex;justify-content:center;pointer-events:none}.fs-code-box{background:rgba(255,255,255,.1);backdrop-filter:blur(5px);padding:.5rem 1rem;border-radius:40px;color:#fff;font-weight:600;font-size:1rem}.toast{position:fixed;bottom:70px;left:50%;transform:translateX(-50%);padding:.7rem 1rem;background:var(--bg-card);border-radius:8px;font-size:.85rem;opacity:0;transition:opacity .2s;z-index:50}.success{border:1px solid #10B981}.error{border:1px solid #EF4444}.show{opacity:1}</style></head><body><div id="splash"><div class="splash-logo"><img src="https://i.ibb.co/Fk3ZvkGX/migroop-logo.jpg"></div><h1 class="splash-title">MIGROPP</h1><p class="splash-subtitle">Copia Compartida</p><div class="splash-loader"><div class="splash-loader-bar"></div></div></div><div id="app"><header class="header"><div class="header-content"><div class="header-logo"><img src="https://i.ibb.co/Fk3ZvkGX/migroop-logo.jpg"><span class="header-title">MIGROPP</span></div></div><div class="search-box"><input type="text" class="search-input" id="searchInput" placeholder="Buscar..."><span class="search-clear" id="clearSearch">X</span></div><div class="stats-bar"><div><strong id="totalCount">0</strong> mat.</div><div><strong id="filteredCount">0</strong> res.</div></div><div class="categories" id="categories"></div></header><main class="material-list" id="materialList"></main></div><div id="detail"><div class="detail-header"><button class="detail-back" id="detailBack">←</button><span style="font-weight:600">Detalle</span></div><div class="detail-image-container" id="detailImageContainer"><img class="detail-image" id="detailImage"></div><div class="detail-content"><div class="detail-row-header"><div class="tag-group"><span class="tag-label">Código</span><span id="detailCode" class="tag-code"></span></div><div class="tag-group right"><span class="tag-label">Categoría</span><span id="detailCat" class="tag-category material-category"></span></div></div><div><span class="tag-label">Descripción</span><div id="detailDesc" class="tag-desc"></div></div><div><span class="tag-label">Ubicación</span><div id="detailLoc" class="tag-location"></div></div><button class="detail-back-btn" id="detailBackBtn">Volver</button></div></div><div id="fullscreen"><button class="fs-close" id="fsClose">X</button><div class="fs-container" id="fsContainer"><img class="fs-img" id="fsImg"></div><div class="fs-code-overlay"><div class="fs-code-box" id="fsCodeOverlay"></div></div></div><div class="toast" id="toast"></div><script>const $=id=>document.getElementById(id);let materials=[],filtered=[],cat='Todos',page=1;const itemsPerPage=20;const INJECTED_DATA="${base64Data}";const DEFAULT_DATA=(function(){try{const json=decodeURIComponent(escape(atob(INJECTED_DATA)));return JSON.parse(json);}catch(e){return[]}})();const getClass=c=>({Estaciones:'cat-Estaciones',Reguladores:'cat-Reguladores',Polietileno:'cat-Polietileno',Acero:'cat-Acero',Galvanizados:'cat-Galvanizados',Medidores:'cat-Medidores',Internas:'cat-Internas',Acometidas:'cat-Acometidas'})[c]||'cat-repherra';function init(){let s=null;try{s=localStorage.getItem('migropp');if(s)s=JSON.parse(s);}catch(e){}if(s&&s.length>0){materials=s;}else if(DEFAULT_DATA.length>0){materials=DEFAULT_DATA;}setTimeout(()=>{$('splash').classList.add('hide');$('app').classList.add('visible');renderCats();render();initZoom();},1200);}function save(){try{localStorage.setItem('migropp',JSON.stringify(materials));}catch(e){}}function renderCats(){const c=['Todos',...new Set(materials.map(m=>m.categoria))];$('categories').innerHTML=c.map(x=>'<span class="category-chip'+(x===cat?' active':'')+'">'+x+'</span>').join('');$('categories').querySelectorAll('.category-chip').forEach((el,i)=>el.onclick=()=>{cat=c[i];renderCats();filter()});}function filter(){const q=$('searchInput').value.toLowerCase();filtered=materials.filter(m=>(cat==='Todos'||m.categoria===cat)&&(m.codigo.toLowerCase().includes(q)||m.descripcion.toLowerCase().includes(q)));$('filteredCount').textContent=filtered.length;render();}function render(){$('totalCount').textContent=materials.length;if(materials.length===0){$('materialList').innerHTML='<p style="text-align:center;color:#6B7A90;padding:2rem">Sin datos</p>';return}if(filtered.length===0){$('materialList').innerHTML='<p style="text-align:center;color:#6B7A90;padding:2rem">No encontrado</p>';return}$('materialList').innerHTML=filtered.map((m,i)=>'<div class="material-card" data-i="'+i+'"><div class="material-image">'+(m.imagen?'<img src="'+m.imagen+'" loading="lazy">':'')+'</div><div class="material-info"><div style="display:flex;justify-content:space-between"><span class="material-code">'+m.codigo+'</span><span class="material-location">'+m.ubicacion+'</span></div><div class="material-name">'+m.descripcion+'</div><span class="material-category '+getClass(m.categoria)+'">'+m.categoria+'</span></div></div>').join('');}$('materialList').onclick=(e)=>{const card=e.target.closest('.material-card');if(!card)return;openDetail(filtered[+card.dataset.i]);};function openDetail(m){$('detailImage').src=m.imagen||'';$('detailCode').textContent=m.codigo;$('detailDesc').textContent=m.descripcion;$('detailCat').className='tag-category material-category '+getClass(m.categoria);$('detailCat').textContent=m.categoria;$('detailLoc').textContent=m.ubicacion;$('detail').classList.add('open');history.pushState({},'');}$('detailBack').onclick=$('detailBackBtn').onclick=()=>history.back();window.onpopstate=()=>{if($('detail').classList.contains('open'))$('detail').classList.remove('open');if($('fullscreen').classList.contains('show'))$('fullscreen').classList.remove('show');};$('searchInput').oninput=()=>{clearTimeout(window.st);window.st=setTimeout(()=>{filter();$('clearSearch').style.display=$('searchInput').value?'block':'none';},150);};$('clearSearch').onclick=()=>{$('searchInput').value='';filter();$('clearSearch').style.display='none';};let fsScale=1,fsX=0,fsY=0,sd=0,ss=1,sx=0,sy=0,drg=false,pn=false;function uT(){$('fsImg').style.transform='translate('+fsX+'px,'+fsY+'px) scale('+fsScale+')';}function rZ(){fsScale=1;fsX=0;fsY=0;uT();}function initZoom(){$('detailImageContainer').onclick=()=>{if(!$('detailImage').src)return;rZ();$('fsImg').src=$('detailImage').src;$('fsCodeOverlay').textContent=$('detailCode').textContent;$('fullscreen').classList.add('show');history.pushState({},'');};$('fsImg').ondblclick=()=>{if(fsScale>1)rZ();else{fsScale=2.5;uT();}};$('fsContainer').ontouchstart=e=>{if(e.touches.length===2){pn=true;sd=Math.hypot(e.touches[0].clientX-e.touches[1].clientX,e.touches[0].clientY-e.touches[1].clientY);ss=fsScale;}else if(e.touches.length===1&&fsScale>1){drg=true;sx=e.touches[0].clientX-fsX;sy=e.touches[0].clientY-fsY;}};$('fsContainer').ontouchmove=e=>{e.preventDefault();if(pn&&e.touches.length===2){const d=Math.hypot(e.touches[0].clientX-e.touches[1].clientX,e.touches[0].clientY-e.touches[1].clientY);fsScale=Math.max(1,Math.min(5,ss*(d/sd)));uT();}else if(drg&&e.touches.length===1){fsX=e.touches[0].clientX-sx;fsY=e.touches[0].clientY-sy;uT();}};$('fsContainer').ontouchend=()=>{drg=false;pn=false;};$('fsContainer').onwheel=e=>{e.preventDefault();fsScale=Math.max(1,Math.min(5,fsScale*(e.deltaY>0?.9:1.1)));if(fsScale===1){fsX=0;fsY=0;}uT();};$('fsClose').onclick=()=>{$('fullscreen').classList.remove('show');rZ();history.back();};}function toast(m){const t=$('toast');t.textContent=m;t.classList.add('show');setTimeout(()=>t.classList.remove('show'),3000);}init();<\/script></body></html>`;
    const blob = new Blob([html], {type: "text/html"});
    const a = document.createElement('a');
    a.href = URL.createObjectURL(blob);
    a.download = 'MIGROPP_Compartido.html';
    a.click();
    URL.revokeObjectURL(a.href);
    toast('Archivo descargado');
};

function toast(m,s=1){const t=$('toast');t.textContent=m;t.className='toast'+(s?' success':' error');t.classList.add('show');setTimeout(()=>t.classList.remove('show'),3000)}
 $('searchInput').oninput=()=>{clearTimeout(searchTimeout);searchTimeout=setTimeout(filter, 150);};

if ('serviceWorker' in navigator) {
    window.addEventListener('load', () => {
        navigator.serviceWorker.register('./sw.js').then(reg => console.log('SW Listo')).catch(err => console.log('SW Fallo', err));
    });
}

init();
</script>
</body>
</html>