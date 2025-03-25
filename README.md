#Modificar el controlador para guardar la foto en el servidor
public function store(Request $request)
{
    $request->validate([
        'nombre_real' => 'required',
        'nombre' => 'required',
        'foto' => 'image|mimes:jpeg,png,jpg,gif|max:2048',
        'informacion' => 'nullable'
    ]);

    if ($request->hasFile('foto')) {
        $ruta_foto = $request->file('foto')->store('public/superheroes');
        $ruta_foto = str_replace('public/', 'storage/', $ruta_foto);
    } else {
        $ruta_foto = null;
    }

    Superheroe::create([
        'nombre_real' => $request->nombre_real,
        'nombre' => $request->nombre,
        'foto' => $ruta_foto,
        'informacion' => $request->informacion
    ]);

    return redirect()->route('superheroes.index');
}
#Este es el método para editar a un superheroe
public function update(Request $request, Superheroe $superheroe)
{
    $request->validate([
        'nombre_real' => 'required',
        'nombre' => 'required',
        'foto' => 'image|mimes:jpeg,png,jpg,gif|max:2048',
        'informacion' => 'nullable'
    ]);

    if ($request->hasFile('foto')) {
        $ruta_foto = $request->file('foto')->store('public/superheroes');
        $ruta_foto = str_replace('public/', 'storage/', $ruta_foto);
        $superheroe->foto = $ruta_foto;
    }

    $superheroe->update($request->except('foto') + ['foto' => $superheroe->foto]);

    return redirect()->route('superheroes.index');
}

#Implementacion de eliminacion logica
public function destroy(Superheroe $superheroe)
{
    $superheroe->delete();
    return redirect()->route('superheroes.index');
}
#Mostrar solo a los superheroes que se encuentran activos
public function index()
{
    $superheroes = Superheroe::whereNull('deleted_at')->get();
    return view('superheroes.index', compact('superheroes'));
}
#Creacion de vista para restaurar a superheroes
public function eliminados()
{
    $superheroes = Superheroe::onlyTrashed()->get();
    return view('superheroes.eliminados', compact('superheroes'));
}
#metodo de restauracion 
public function restaurar($id)
{
    $superheroe = Superheroe::withTrashed()->find($id);
    $superheroe->restore();

    return redirect()->route('superheroes.index');
}
#rutas de web php
Route::get('superheroes/eliminados', [SuperheroeController::class, 'eliminados'])->name('superheroes.eliminados');
Route::post('superheroes/{id}/restaurar', [SuperheroeController::class, 'restaurar'])->name('superheroes.restaurar');


#vista de eliminados
@extends('layouts.app')

@section('content')
<h1>Superhéroes Eliminados</h1>

<table>
    <tr>
        <th>Nombre</th>
        <th>Acciones</th>
    </tr>
    @foreach($superheroes as $superheroe)
    <tr>
        <td>{{ $superheroe->nombre }}</td>
        <td>
            <form action="{{ route('superheroes.restaurar', $superheroe->id) }}" method="POST">
                @csrf
                <button type="submit">Restaurar</button>
            </form>
        </td>
    </tr>
    @endforeach
</table>

<a href="{{ route('superheroes.index') }}">Volver a la lista</a>
@endsection
