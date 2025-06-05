---


---

<h1 id="hướng-dẫn-setup-n8n-cpanel">Hướng dẫn setup n8n cpanel</h1>
<p>Hướng dẫn setup n8n cpanel với pm2.</p>
<h2 id="setup-nodejs-và-pm2-n8n">Setup nodejs và pm2, n8n</h2>
<h3 id="tải-và-chạy-script-cài-đặt-nvm">Tải và chạy script cài đặt NVM</h3>
<pre class=" language-bash"><code class="prism  language-bash">curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh <span class="token operator">|</span> <span class="token function">bash</span>
</code></pre>
<h3 id="sau-khi-chạy-script-bạn-cần-tải-lại-shell-hoặc-khởi-động-lại-terminal-để-nvm-có-hiệu-lực.">Sau khi chạy script, bạn cần tải lại shell hoặc khởi động lại Terminal để NVM có hiệu lực.</h3>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">source</span> ~/.bashrc
</code></pre>
<h3 id="kiểm-tra-nvm">Kiểm tra nvm</h3>
<pre><code>nvm -v
</code></pre>
<p>Nếu lệnh trả về <code>nvm</code>, tức là NVM đã được cài đặt thành công.</p>
<h3 id="cài-đặt-node.js-bằng-nvm">Cài đặt Node.js bằng NVM</h3>
<p>Sử dụng NVM để cài đặt phiên bản Node.js mong muốn (ví dụ: phiên bản LTS mới nhất).</p>
<pre><code>nvm install --lts 
nvm use --lts
</code></pre>
<p>Hoặc cài đặt một phiên bản cụ thể:</p>
<pre><code>nvm install 20 # Cài đặt Node.js phiên bản 20 
nvm use 20 # Sử dụng Node.js phiên bản 20
</code></pre>
<h3 id="kiểm-tra-node.js-và-npm">Kiểm tra Node.js và npm:</h3>
<pre><code>node -v 
npm -v
</code></pre>
<h3 id="tạo-một-thư-mục-mới-cho-n8n-trong-thư-mục-gốc-của-bạn.">Tạo một thư mục mới cho n8n trong thư mục gốc của bạn.</h3>
<pre><code>mkdir ~/n8n_app
cd ~/n8n_app
</code></pre>
<h3 id="khởi-tạo-package.json-và-cài-n8n">Khởi tạo package.json và cài n8n</h3>
<pre><code>npm init -y
npm install n8n@latest --save
</code></pre>
<h3 id="cài-pm2-toàn-cục">Cài pm2 toàn cục</h3>
<pre><code>npm install -g pm2
</code></pre>
<h3 id="tạo-file-ecosystem-để-chạy-pm2">Tạo file ecosystem để chạy pm2</h3>
<pre><code>nano ecosystem.config.js
</code></pre>
<h3 id="dán-nội-dung-sau-vào">Dán nội dung sau vào</h3>
<pre class=" language-json"><code class="prism  language-json">module<span class="token punctuation">.</span>exports <span class="token operator">=</span> <span class="token punctuation">{</span>
  apps<span class="token punctuation">:</span> <span class="token punctuation">[</span>
    <span class="token punctuation">{</span>
      name<span class="token punctuation">:</span> <span class="token string">"n8n"</span><span class="token punctuation">,</span>
      script<span class="token punctuation">:</span> <span class="token string">"./node_modules/n8n/bin/n8n"</span><span class="token punctuation">,</span>
      env<span class="token punctuation">:</span> <span class="token punctuation">{</span>
        DB_TYPE<span class="token punctuation">:</span> <span class="token string">'sqlite'</span><span class="token punctuation">,</span> <span class="token comment">// chạy với sqlite</span>
        N8N_RELEASE_TYPE <span class="token punctuation">:</span> <span class="token string">'stable'</span><span class="token punctuation">,</span> <span class="token comment">// stable thì logo đỏ và ko có chữ Dev</span>
        N8N_BASIC_AUTH_ACTIVE<span class="token punctuation">:</span> <span class="token string">"true"</span><span class="token punctuation">,</span> 
        WEBHOOK_URL<span class="token punctuation">:</span> <span class="token string">"https://hoang.io.vn/n8n"</span><span class="token punctuation">,</span>
        N8N_DIAGNOSTICS_ENABLED<span class="token punctuation">:</span> <span class="token string">"false"</span><span class="token punctuation">,</span>
        N8N_SKIP_WEBHOOK_DEREGISTRATION_SHUTDOWN<span class="token punctuation">:</span> <span class="token string">"true"</span><span class="token punctuation">,</span>
        N8N_HOST<span class="token punctuation">:</span> <span class="token string">'127.0.0.1'</span><span class="token punctuation">,</span>
        N8N_PORT<span class="token punctuation">:</span> <span class="token number">5678</span><span class="token punctuation">,</span>
        N8N_PATH<span class="token punctuation">:</span> <span class="token string">'/n8n/'</span><span class="token punctuation">,</span> <span class="token comment">//path</span>
        N8N_EDITOR_BASE_URL<span class="token punctuation">:</span> <span class="token string">'https://hoang.io.vn/n8n/'</span><span class="token punctuation">,</span>
        N8N_PUSH_BACKEND<span class="token punctuation">:</span> <span class="token string">'sse'</span> <span class="token comment">// loại push BE từ FE, có thể là ws</span>
      <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
  <span class="token punctuation">]</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<p><mark>Ấn Ctrl X, gõ y =&gt; enter để lưu</mark></p>
<h3 id="chạy-n8n">Chạy n8n</h3>
<pre><code>pm2 start ecosystem.config.js
</code></pre>
<h3 id="cấu-hình-reverse-proxy-.htaccess-file">Cấu hình reverse proxy (.htaccess file)</h3>
<pre><code>nano ~/public_html/.htaccess
</code></pre>
<h3 id="dán-cấu-hình-sau-vào">Dán cấu hình sau vào</h3>
<pre><code>RewriteEngine On
ProxyPreserveHost On

# WebSocket (nếu cần)
RewriteCond %{HTTP:Upgrade} =websocket [NC]
RewriteRule ^n8n/ws/(.*)$ ws://127.0.0.1:5678/ws/$1 [P,L]

# Proxy /n8n hoặc /n8n/ → backend "/"
RewriteRule ^n8n/?$ http://127.0.0.1:5678/ [P,L]

# Proxy mọi URL /n8n/... → backend "/..."
RewriteRule ^n8n/(.*)$ http://127.0.0.1:5678/$1 [P,L]

RequestHeader set Origin “https://domain.chay.n8n”
RequestHeader set X-Forwarded-Proto https
</code></pre>
<p>Lưu lại và truy cập vào url để xem.</p>

