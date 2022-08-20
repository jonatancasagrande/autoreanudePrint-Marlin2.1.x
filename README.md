SOLUCIÓN PARA AUTOREANUDACIÓN DE IMPRESIONES FRENTE A CORTE DE SUMINISTRO – LANGUAGE: SPANISH

Código desarrollado en colaboración con https://www.facebook.com/emiliano.cardelli/. Gracias por tu tiempo y dedicación!

Problema: la impresora BIQU B1 y todas aquellas que implementen MARLIN junto a un sistema de SAI UPS tienen la capacidad de previa configuración poder almacenar un fichero de nombre PLR con datos referidos al fichero en impresión, altura de capa y otros parámetros. Luego al volver el suministro uno debe confirmar de modo manual si desea retomar dicha impresión o no. En una granja de impresoras esto además de costoso en tiempo puede no ser tan práctico ya que se desea tener los equipos imprimiendo 24/7.

Solución: para dar con la misma inicialmente se buscó la porción de código dedicada a la función powerloss, una vez localizada se re-programo con la modificación de utilizar un fichero de nombre “CONT” sin extensión que al estar presente en la microSD y volver el suministro eléctrico, logra de modo automático la reanudación de la impresión. Para volver al modo normal (en donde el usuario debe confirmar la reanudación) solamente basta con eliminar o re nombrar dichero fichero “CONT”.

El fichero powerloss.cpp está ubicado dentro de Marlin/src/feature/powerloss.cpp

/**
 * Check for Print Job Recovery during setup()
 *
 * If a saved state exists send 'M1000 S' to initiate job recovery.
 */
 
bool PrintJobRecovery::check() {
  //if (!card.isMounted()) card.mount();
  //gcode.process_subcommands_now("");
      bool success = false;
      if (card.isMounted()) {
        load();
        success = valid();
        if (!success)
          cancel();
        else
          
          if(card.fileExists("CONT"))
          {
            queue.inject_P(PSTR("M1000"));
          }
          else
          {
             queue.inject_P(PSTR("M1000S"));
          }
      }
      return success;       
  
}
