# Ayudantía Ingeniería de Software: CRUD + Routing

## Primera Parte: Routing Simple

```ruby
rails g controller Pages main
```

### `pages_controller.rb`
```ruby
class PagesController < ApplicationController
  def main
    render 'main'
  end
  def info
    render 'info'
  end
end
```

### `main.html.erb`
```html
<h1>Hello world</h1>
<p>Esto es un ejemplo simple de routing para una aplicación Hello World, probablemente ya lo hicieron para su entrega 0.</p>
```

### `info.html.erb`
```html
<h1>Info</h1>
<p>Esta aplicación fue creada para la ayudantía de Rails: CRUD + Routing<p>

```

### `routes.rb`
```ruby
Rails.application.routes.draw do
  
  # Estos dos son equivalentes
  get '/', to: 'pages#main'
  root to: 'pages#main'

  get 'info', to: 'pages#info'
end

```


## Segunda Parte: CRUD

```ruby
rails g model Pelicula titulo:string ano:integer director:string
```

```ruby
rails g controller Peliculas index show new edit
```

### routes.rb
```ruby
Rails.application.routes.draw do
  
  # Estos dos son equivalentes
  get '/', to: 'pages#main'
  root to: 'pages#main'

  get 'info', to: 'pages#info'


  # Rutas del CRUD
  
  # Create
  get 'peliculas/new', to: 'peliculas#new'
  post 'peliculas', to: 'peliculas#create'

  # Read
  get 'peliculas', to: 'peliculas#index'
  get 'peliculas/:id', to: 'peliculas#show', as: :pelicula

  # Update
  get 'peliculas/:id/edit', to: 'peliculas#edit', as: :peliculas_edit
  patch 'peliculas/:id/', to: 'peliculas#update'
  put 'peliculas/:id/', to: 'peliculas#update'

  # Delete
  delete 'peliculas/:id', to: 'peliculas#destroy'

end

```

### peliculas_controller.rb
```ruby
class PeliculasController < ApplicationController
  

  def index
    @peliculas = Pelicula.all
  end


  def show
    @pelicula = Pelicula.find(params[:id])
  end


  def new
    @pelicula = Pelicula.new
  end


  def create
    pelicula_params = params.require(:pelicula).permit(:titulo, :ano, :director)
    @pelicula = Pelicula.create(pelicula_params)

    if @pelicula.save
      redirect_to pelicula_path(@pelicula.id), notice: 'Película creada con éxito'
    else
      redirect_to peliculas_new_path, notice: 'Ocurrió un error al crear la película.'
    end

  end


  def edit
    @pelicula = Pelicula.find(params[:id])
  end


  def update
    pelicula_params = params.require(:pelicula).permit(:titulo, :ano, :director)
    @pelicula = Pelicula.find(params[:id])

    if @pelicula.update(pelicula_params)
      redirect_to pelicula_path(@pelicula.id), notice: 'Película editada con éxito'
    else
      redirect_to peliculas_new_path, notice: 'Ocurrió un error al editar la película.'
    end
  end


  def destroy
    @pelicula = Pelicula.find(params[:id])
    @pelicula.destroy
    redirect_to peliculas_path, notice: 'Película eliminada con éxito'
  end

  
end

```

### new.html.erb
```ruby
<h1>Nueva Pelicula</h1>

<%= form_with(model: @pelicula, method: :post) do |form| %>
  <div class="field">
    <%= form.label :titulo %>
    <%= form.text_field :titulo %>
  </div>

  <div class="field">
    <%= form.label :ano %>
    <%= form.number_field :ano %>
  </div>

  <div class="field">
    <%= form.label :director %>
    <%= form.text_field :director %>
  </div>

  <div class="actions">
    <%= form.submit %>
  </div>
<% end %>

<%= link_to 'Back', peliculas_path %> 
```

### edit.html.erb
```ruby
<h1>Editar Pelicula</h1>

<%= form_with(model: @pelicula, url: pelicula_path(@pelicula.id), method: :patch) do |form| %>
  <div class="field">
    <%= form.label :titulo %>
    <%= form.text_field :titulo %>
  </div>

  <div class="field">
    <%= form.label :ano %>
    <%= form.number_field :ano %>
  </div>

  <div class="field">
    <%= form.label :director %>
    <%= form.text_field :director %>
  </div>

  <div class="actions">
    <%= form.submit %>
  </div>
<% end %>

<%= link_to 'Back', peliculas_path %> 

```

### index.html.erb
```html
<p id="notice"><%= notice %></p>

<h1>Peliculas</h1>

<table>
  <thead>
    <tr>
      <th>Título</th>
      <th>Ano</th>
      <th>Director</th>
      <th colspan="3"> Acciones </th>
    </tr>
  </thead>

  <tbody>
    <% @peliculas.each do |pelicula| %>
      <tr>
        <td><%= pelicula.titulo %></td>
        <td><%= pelicula.ano %></td>
        <td><%= pelicula.director %></td>
        <td><%= link_to 'Ver', pelicula_path(pelicula.id) %>
      </tr>
    <% end %>
  </tbody>
</table>

<br>

<%= link_to 'Crear Pelicula', peliculas_new_path %>
```

### show.html.erb
```html
<p id="notice"><%= notice %></p>

<p>
  <strong>Título:</strong>
  <%= @pelicula.titulo %>
</p>

<p>
  <strong>Ano:</strong>
  <%= @pelicula.ano %>
</p>

<p>
  <strong>Director:</strong>
  <%= @pelicula.director %>
</p>

<%= link_to 'Editar Película', peliculas_edit_path(@pelicula.id) %>
<%= link_to 'Eliminar Película', pelicula_path(@pelicula.id), method: :delete, alert: 'Estás seguro?' %>
<%= link_to 'Lista Películas', peliculas_path %>
```
