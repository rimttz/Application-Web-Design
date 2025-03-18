#Definicón del modelo
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Superheroe extends Model
{
    protected $fillable = [
        'nombre_real', 
        'nombre_superheroe', 
        'foto_url', 
        'informacion_adicional'
    ];
}

#Creacion de rutas
use App\Http\Controllers\SuperheroeController;

Route::resource('superheroes', SuperheroeController::class);

#CRUD en el controlador

<?php
namespace App\Http\Controllers;

use App\Models\Superheroe;
use Illuminate\Http\Request;

class SuperheroeController extends Controller
{
    public function index() {
        $superheroes = Superheroe::all();
        return view('superheroes.index', compact('superheroes'));
    }

    public function create() {
        return view('superheroes.create');
    }

    public function store(Request $request) {
        Superheroe::create($request->all());
        return redirect()->route('superheroes.index')->with('success', '¡Superhéroe registrado con éxito!');
    }

    public function show(Superheroe $superheroe) {
        return view('superheroes.show', compact('superheroe'));
    }

    public function edit(Superheroe $superheroe) {
        return view('superheroes.edit', compact('superheroe'));
    }

    public function update(Request $request, Superheroe $superheroe) {
        $superheroe->update($request->all());
        return redirect()->route('superheroes.index')->with('success', '¡Superhéroe actualizado con éxito!');
    }

    public function destroy(Superheroe $superheroe) {
        $superheroe->delete();
        return redirect()->route('superheroes.index')->with('success', '¡Superhéroe eliminado con éxito!');
    }
}

#Creacion de vistas (index.blade.php)
@extends('layouts.app')

@section('content')
    <h1>Lista de Superhéroes</h1>
    <a href="{{ route('superheroes.create') }}">Añadir Superhéroe</a>

    @foreach ($superheroes as $superheroe)
        <p>{{ $superheroe->nombre_superheroe }} ({{ $superheroe->nombre_real }})</p>
    @endforeach
@endsection

#(create.blade.php)
@extends('layouts.app')

@section('content')
    <h1>Registrar Superhéroe</h1>

    <form method="POST" action="{{ route('superheroes.store') }}">
        @csrf
        <label>Nombre Real:</label>
        <input type="text" name="nombre_real" required>
        
        <label>Nombre de Superhéroe:</label>
        <input type="text" name="nombre_superheroe" required>

        <label>URL de la Foto:</label>
        <input type="text" name="foto_url" required>

        <label>Información Adicional:</label>
        <textarea name="informacion_adicional"></textarea>

        <button type="submit">Guardar</button>
    </form>
@endsection
