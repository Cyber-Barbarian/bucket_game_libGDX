# 1 - Início do projeto 
Crie um projeto no GDX-Liftoff com as seguintes configurações:
 - Nome do Projeto: bucket_game_libGDX
 - Pacote: com.badlogic.drop
 - Classe Principal: Main
 - Platforms:  Core, Desktop, Android platforms
 - Template: ApplicationListener
 - Em settings: selecionar a versão do java e do android sdk, bem como o path do projeto
 - abrir no Intelij IDEA e compilar o projeto

Em [Lwjgl3Launcher.java](..%2Flwjgl3%2Fsrc%2Fmain%2Fjava%2Fcom%2Fbadlogic%2Fdrop%2Flwjgl3%2FLwjgl3Launcher.java) mudamos a tela para 800X500
```java
configuration.setWindowedMode(800, 500);
```
Em [Main.java](..%2Fcore%2Fsrc%2Fmain%2Fjava%2Fcom%2Fbadlogic%2Fdrop%2FMain.java) alteramos o método Render() mara fazer a limpeza sempre que for chamado
```java
   @Override
public void render() {
    // Draw your application here.
    ScreenUtils.clear(Color.BLUE);
}
```
Ao rodar o projeto poderemos ver que funciona

# 2 - Trazendo os assets 
- Vamos salval os asstes de imagem e audio na pasta assets  [assets](..%2Fassets)
- Na classe Main, vamos declarar as variáveis desses assets [Main.java](..%2Fcore%2Fsrc%2Fmain%2Fjava%2Fcom%2Fbadlogic%2Fdrop%2FMain.java)
```java
public class Main implements ApplicationListener {
    Texture backgroundTexture;
    Texture bucketTexture;
    Texture dropTexture;
    Sound dropSound;
    Music music;
    ...;}
```
- Os construtores devem ser criados dentro do método create() para que o libGDX os carregue
```java 
@Override
public void create() {
    backgroundTexture = new Texture("background.png");
    bucketTexture = new Texture("bucket.png");
    dropTexture = new Texture("drop.png");
    dropSound = Gdx.audio.newSound(Gdx.files.internal("drop.mp3"));
    music = Gdx.audio.newMusic(Gdx.files.internal("music.mp3"));
}

```
# 3 - Renderizando os assets
- Aqui duas classes são fundamentais: FitViewport e SpriteBatch 
- A viewport controla como vemos o jogo. FitViewport irá garantir que não importa o tamanho da nossa janela, a visualização completa do jogo estará sempre visível.
- O SpriteBatch é como a libGDX combina essas chamadas gráficas.
```java
public class Main implements ApplicationListener {
    ...

    SpriteBatch spriteBatch;
    FitViewport viewport;

    @Override
    public void create() {
    ...

        spriteBatch = new SpriteBatch();
        viewport = new FitViewport(8, 5);
    }
}
```
- Você deve sempre lembrar de atualizar a viewport no método de resize():
```java
@Override
public void resize(int width, int height) {
    viewport.update(width, height, true); // true centers the camera
}

```
- Nosso código ficará mais complexo agora. Uma boa prática é o código em métodos separados. Vamos nos preparar para isso adicionando novos métodos a serem usados dentro do método render():
- focando no método draw(), trazemos `ScreenUtils.clear(Color.BLUE);` para limpar nossa tela antes de renderizar os demais gráficos
- Já o `spriteBatch.setProjectionMatrix(viewport.getCamera().combined)` mostra como o Viewport é aplicado ao SpriteBatch. Isso é necessário para que as imagens sejam mostradas no local correto;
- `begin()` e `end()`demarcam o que será renderizado no spriteBatch , no nosso caso o background e o personagem, na ordem de declaração
- Portanto, é importante que se renderize primeiro o background e depois o personagem para que este apareça na frente do primeiro
- Criamos também as variáveis worldWidth e worldHeight para controlar o tamanho do background na janela

```java
@Override
public void render() {
    // organize code into three methods
    input();
    logic();
    draw();
}

private void input() {

}

private void logic() {

}

private void draw() {
    ScreenUtils.clear(Color.BLUE);
    viewport.apply();
    spriteBatch.setProjectionMatrix(viewport.getCamera().combined);
    spriteBatch.begin();
//objetos renderizados
    float worldWidth = viewport.getWorldWidth();
    float worldHeight = viewport.getWorldHeight();
    //spriteBatch.draw(textura, posiçãox, posiçãoy, largura altura)
    spriteBatch.draw(backgroundTexture, 0, 0, worldWidth, worldHeight);
    spriteBatch.draw(bucketTexture, 0,0,1,1);
    spriteBatch.end();
}

 
```
- Se rodarmos o projeto, veremos esses assets.
