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
