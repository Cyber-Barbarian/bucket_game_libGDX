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

# 4 - Sprites Controles
## Sprites
- Quando utilizamos texuras estamos lidando com imagens estáticas, que não guardam posição
- Para implementar movimento, no lugar de textura devemos então usar `Sprite`  para nosso personagem
```java
public class Main implements ApplicationListener {
    ...
    Sprite bucketSprite; // Declare a new Sprite variable
}
@Override
public void create() {
    ...
    bucketSprite = new Sprite(bucketTexture); // Initialize the sprite based on the texture
    bucketSprite.setSize(1, 1); // Define the size of the sprite
}

```

- Devemos também substituir a maneira como se "desenha" o Sprite. Sprites são desenhados de maneira diferente de textura
```java
private void draw() {
    ScreenUtils.clear(Color.BLACK);
    viewport.apply();
    spriteBatch.setProjectionMatrix(viewport.getCamera().combined);
    spriteBatch.begin();

    float worldWidth = viewport.getWorldWidth();
    float worldHeight = viewport.getWorldHeight();

    spriteBatch.draw(backgroundTexture, 0, 0, worldWidth, worldHeight);
    bucketSprite.draw(spriteBatch); // Sprites have their own draw method

    spriteBatch.end();
}

```
## Controle teclado
- Como ainda não temos controles implementados, não veremos diferença ao rodar o projeto
- Para implementar a movimentação via teclado, usaremos o método `input()`. 
- Vamos pensar primeiramente em movimentação para a direita e para a esquerda.
- criamos uma variável de movimento chamada `speed` para a velocidade e uma variável de delta chamada `delta` para o tempo, de forma a obter a mudança de posição por meio da multiplicação de ambas
- Gdx.input.isKeyPressed verifica se a tecla está sendo pressionada
- Gdx.graphics.getDeltaTime(), fornece o tempo decorrido desde o último quadro renderizado. Ao ser multiplicado por `speed`, temos uma nova posição do personagem
```java
private void input() {
    float speed = 4f;
    float delta = Gdx.graphics.getDeltaTime(); // retrieve the current delta

    if (Gdx.input.isKeyPressed(Input.Keys.RIGHT)) {
        bucketSprite.translateX(speed * delta); // move the bucket right
    } else if (Gdx.input.isKeyPressed(Input.Keys.LEFT)) {
        bucketSprite.translateX(-speed * delta); // move the bucket left
    }
}

```
## Controle via mouse e touch
- Controle via mous e via touch são correlatos. Ambos usam o método `isTouched()` para detectar se houve click ou algum toque na tela
- para detectar a posição do toque precisamos criar um objeto do tipo `Vector2` para encapsular um vetor 2d com a posição do toque
```java
public class Main implements ApplicationListener {
    ...
    Vector2 touchPos;
    ...
    @Override
    public void create() {
    ...

        touchPos = new Vector2();
    }
}

```
- com o Vector2 podemos saber onde ocorreu o toque e mover o personagem para a direita ou para a esquerda 
- vamos captar somente a posição x do toque , que é o que nos interessa, da seguinte maneira
```java
private void input() {
    float speed = 4f;
    float delta = Gdx.graphics.getDeltaTime();

    if (Gdx.input.isKeyPressed(Input.Keys.RIGHT)) {
        bucketSprite.translateX(speed * delta);
    } else if (Gdx.input.isKeyPressed(Input.Keys.LEFT)) {
        bucketSprite.translateX(-speed * delta);
    }

    if (Gdx.input.isTouched()) {
        touchPos.set(Gdx.input.getX(), Gdx.input.getY()); //  Captamos a posição do toque
        viewport.unproject(touchPos); // Convertemoos para unidades normalizadas na viewport
        bucketSprite.setCenterX(touchPos.x); // mudamos o personagem horizontalmente
    }
}

```
# 5 - Lógica do jogo

## Limites
- um ponto precisa ser ajustado em nosso jogo: nosso personagem consegue sair do limite da tela e voltar
- Para resolver isso, precisamos criar uma nova variável chamada `worldWidth` e `worldHeight` para controlar o tamanho do background na janela
- Vamos criar também `bucketWidth` e `bucketHeight` para controlar o tamanho do personagem
- com o método MathUtils.clamp podemos limitar o movimento do personagem na tela estabelecendo limites no eixo X
- clamp fará com que o valor de x fique sempre entre máximos e mínimos trazendo o valor dos limites caso o getX() seja maior ou menor que os limites
```java
private void logic() {
    float worldWidth = viewport.getWorldWidth();
    float worldHeight = viewport.getWorldHeight();

    // Store the bucket size for brevity
    float bucketWidth = bucketSprite.getWidth();
    float bucketHeight = bucketSprite.getHeight();

    // Subtract the bucket width
    bucketSprite.setX(MathUtils.clamp(bucketSprite.getX(), 0, worldWidth - bucketWidth));
}

```
## Lógica dos objetos
- Um primeiro ponto para entendermos é que nossas gotas de chuva não serão somente um Sprite, pois serão renderizadas muitasao mesmo tempo
- Elas serão um array de Sprites chamado `dropsSprites`, declarado e iniciado da seguinte maneira
```java
public class Main implements ApplicationListener {
    ...
    Array<Sprite> dropSprites;

    public void create() {
    ...

        dropSprites = new Array<>();
    }
}
```
- como serão criados muitos sprites para as gotas, convém colocar um método a parte a criação desses sprites 
- esse método insere no array `dropSprites` o novo sprite, que tem  tamanho(1,1), e posição (0,altura máxima)
```java
public void create() {
    ...
    dropSprites = new Array<>();

    createDroplet();
}
...
private void draw() {
    ...
}

private void createDroplet() {
    // create local variables for convenience
    float dropWidth = 1;
    float dropHeight = 1;
    float worldWidth = viewport.getWorldWidth();
    float worldHeight = viewport.getWorldHeight();
    
    // create the drop sprite
    Sprite dropSprite = new Sprite(dropTexture);
    dropSprite.setSize(dropWidth, dropHeight);
    dropSprite.setX(0);
    dropSprite.setY(worldHeight);
    dropSprites.add(dropSprite); // Add it to the list
}

@Override
public void pause() {

}

```
- falta desenhar as goras em draw() e colocar sua lógica de movimento em logic(). Isso funciona com simples loops
```java
private void draw() {
    ...
    spriteBatch.begin();

    ...

    // draw each sprite
    for (Sprite dropSprite : dropSprites) {
        dropSprite.draw(spriteBatch);
    }

    spriteBatch.end();
}

private void logic() {
    ...

    // loop through each drop
    float dropSpeed = -2f;
    float delta = Gdx.graphics.getDeltaTime();

    for (Sprite dropSprite : dropSprites) {
        dropSprite.translateY(dropSpeed * delta);
    }
}

```
- Se rodarmos o projeto agora poderemos ver uma gota é renderizada uma única vez e cai na esquerda da tela
- isso ocorre porque nosso `createDroplet()` está dentro do método create(), logo ele roda uma única vez no início do programa
- além disso setamos a posiição horizontal em 0 manualmente no `createDroplet()`
- primeiramente amos mover o `createDroplet()` para dentro do metodo `logic()` para ser acionado a cada quadro

```java
private void logic() {
        float viewportWidth = viewport.getWorldWidth();
        float viewportHeight = viewport.getWorldHeight();

        float bucketWidth = bucketSprite.getWidth();
        float bucketHeight = bucketSprite.getHeight();

        bucketSprite.setX(MathUtils.clamp(bucketSprite.getX(), 0, viewportWidth - bucketWidth));

        float dropSpeed = -2f;
        float delta = Gdx.graphics.getDeltaTime();

        for (Sprite dropSprite : dropSprites) {
            dropSprite.translateY(dropSpeed * delta);
        }

        createDroplet();
    }
```
- Vamos ver que a cada frame uma gota é gerada, produzindo assim muitíssimas gotas por segundo, e que elas cairão na esquerda da tela
- vamos encapsular nosso `createDroplet()` em um condicional para que seja chamado apenas uma vez a cada segundo
```java
public class Main implements ApplicationListener {
    ...
    float dropTimer;


private void logic() {
    ...
    dropTimer += delta;
    if (dropTimer >= 1.5f) {
        createDroplet();
        dropTimer = 0f;
    }
}
 
```
- Agora, a cada 1,5 segundos uma nova gota será gerada na esquerda. vamos acertar a posição x de cada gota gerada para uma posição aleatória entre 0 e o tamanho da tela usando o método `MathUtils.random()`
```java
private void createDroplet() {
    // create local variables for convenience
    float dropWidth = 1f;
    float dropHeight = 1f;
    float worldWidth = viewport.getWorldWidth();
    float worldHeight = viewport.getWorldHeight();

    // create the drop sprite
    Sprite dropSprite = new Sprite(dropTexture);
    dropSprite.setSize(dropWidth, dropHeight);
    dropSprite.setX(MathUtils.random(0f, worldWidth - dropWidth));
    dropSprite.setY(worldHeight);
    dropSprites.add(dropSprite); // Add it to the list
} 
```
## Lidando com a memória
- Se monitorarmos a memória de nosso jogo verificaremos que teremos um problema de increento de memória
- Isso ocorre por não estarmos "destruindo" as gotas que foram criadas.
- Em outras palavras, nosso array de gotas cresce sem parar. se colocarmos um `System.out.println(dropSprites.size);` do fim de `createDroplet()`poderemos ver isso no terminal
- Vamos então criar uma lógica onde se uma gota atinge o fundo da tela ela é removida do array
- fizemos um incremento em private void `logic()` para seja removido do array o elemento que sai da tela
```java
private void logic() {
...
float dropHeight = 1f;

        for (Sprite dropSprite : dropSprites) {
            dropSprite.translateY(dropSpeed * delta);
            if (dropSprite.getY()< -dropHeight) {
                dropSprites.removeValue(dropSprite, true);
            }
        }
```
