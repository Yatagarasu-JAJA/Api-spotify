# Spotify Buscador (Flutter)

App que busca canciones con la API REST de Spotify (`GET /v1/search`), las muestra en una lista y permite ver el detalle.

## Cómo ejecutarla
1. `flutter create spotify_buscador` y reemplaza `lib/` y `pubspec.yaml` por los de este proyecto.
2. En el Spotify Developer Dashboard crea una app y copia el Client ID y el Client Secret
   (el dueño de la app debe tener Spotify Premium para apps en Development Mode).
3. Edita `lib/core/config/spotify_config.dart` con tus credenciales.
   Agrega esa ruta al `.gitignore` para no subir el secreto a GitHub (ver `.gitignore.add`).
4. `flutter pub get` y luego `flutter run` (usa un emulador o celular Android; en web falla por CORS).

## Android
En `android/app/src/main/AndroidManifest.xml`, dentro de `<manifest>` y antes de `<application>`:
```xml
<uses-permission android:name="android.permission.INTERNET"/>
<queries>
  <intent>
    <action android:name="android.intent.action.VIEW"/>
    <data android:scheme="https"/>
  </intent>
</queries>
```

## Arquitectura (Feature First)
```
lib/
├── main.dart
├── core/                     (código compartido por toda la app)
│   ├── config/               credenciales de Spotify
│   ├── api/                  obtención del token
│   └── errors/               ApiError
└── features/
    └── canciones/            (una carpeta por funcionalidad)
        ├── data/             servicio HTTP y modelo Track (fromJson)
        └── presentation/     pages (pantallas) y widgets
```

## Flujo
BusquedaPage -> SpotifyService.buscarCanciones -> SpotifyAuthService (token) -> GET /v1/search
-> 200: jsonDecode -> Track.fromJson -> ListView / otro código: ApiError -> EstadoError (Reintentar).

## Audio
La pantalla de detalle reproduce una vista previa solo cuando Spotify incluye `preview_url`
en el resultado. No todas las canciones tienen vista previa y esta app no reproduce canciones
completas. El botón **Abrir en Spotify** permite abrir la canción en Spotify.
