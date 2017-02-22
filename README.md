# Inventory-Management
package inventorymanager;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.PrintStream;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Scanner;
import javafx.application.Application;
import javafx.application.Platform;
import javafx.collections.FXCollections;
import javafx.collections.ObservableList;
import javafx.geometry.Insets;
import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.ListView;
import javafx.scene.control.TextField;
import javafx.scene.layout.BorderPane;
import javafx.scene.layout.GridPane;
import javafx.scene.layout.HBox;
import javafx.scene.layout.StackPane;
import javafx.scene.text.Text;
import javafx.stage.FileChooser;
import javafx.stage.Stage;

// Written by: Taylor Vearil and Justin Stevenor
// This project can take user imput to update inventory information
// then display it in multiple ways.
public class InventoryManager extends Application {

    public static BorderPane root = new BorderPane();
    public static Entry[] entryList = new Entry[200];
    public static int number = 0;
    public static int id = 0;

    @Override
    public void start(Stage primaryStage) throws FileNotFoundException {
        InventoryActions action = new InventoryActions();
        root.setTop(getHBox(action, primaryStage));
        root.setCenter(new Text("Welcome to the inventory manager"));
        Scene scene = new Scene(root, 600, 600);

        primaryStage.setTitle("Inventory Management FX");
        primaryStage.setScene(scene);
        primaryStage.show();
    }

    private HBox getHBox(InventoryActions action, Stage prime) throws FileNotFoundException {
        HBox hBox = new HBox(15);
        hBox.setPadding(new Insets(15, 15, 15, 15));
        makeButtons("Load", hBox, action, prime);
        makeButtons("New Entry", hBox, action, prime);
        makeButtons("List", hBox, action, prime);
        makeButtons("Save", hBox, action, prime);
        TextField text = new TextField();
        text.setPromptText("Search Products");
        Button finder = new Button("Find");
        finder.setOnMouseClicked(e -> {
            action.Find(text.getCharacters(), entryList);
        });
        hBox.setAlignment(Pos.CENTER);
        hBox.getChildren().addAll(text, finder);
        makeButtons("Quit", hBox, action, prime);
        return hBox;
    }

    private void makeButtons(String title, HBox box, InventoryActions action, Stage prime) throws FileNotFoundException {
        // Add Methods to each event handler to perform.
        Button button = new Button();
        button.setText(title);
        switch (title) {
            case "New Entry":
                button.setOnMouseClicked(e -> {
                    root.setCenter(action.Entry(entryList));
                });
                break;
            case "List":
                button.setOnMouseClicked(e -> {
                    root.setCenter(action.List(entryList));
                });
                break;
            case "Save":
                button.setOnMouseClicked(e -> {
                    FileChooser fileChooser = new FileChooser();

                    FileChooser.ExtensionFilter extFilter = new FileChooser.ExtensionFilter("TXT files (*.txt)", "*.txt");
                    fileChooser.getExtensionFilters().add(extFilter);

                    File filew = fileChooser.showSaveDialog(prime);

                    if (filew != null) {
                        action.Save(entryList, filew);
                    }
        });

        break;
    

    case "Load":
                button.setOnMouseClicked(e ->{
                    FileChooser fileChooser = new FileChooser();

                    FileChooser.ExtensionFilter extFilter = new FileChooser.ExtensionFilter("TXT files (*.txt)", "*.txt");
                    fileChooser.getExtensionFilters().add(extFilter);

                    File filew = fileChooser.showOpenDialog(prime);
                    if (filew != null) {
                        action.Load(entryList, filew);
                    }
                }); 
                break;
            case "Quit":
                button.setOnMouseClicked(e -> {
                    Platform.exit();
                });
                break;
            default:
                button.setOnMouseClicked(e -> {
                    System.out.println("Unkown Error!");
                });
                break;
        }

    box.getChildren ()

.add(button);
    }

    public static void main(String[] args) {
        launch(args);
    

}

}

class InventoryActions {

    private StackPane pane = new StackPane();
    private int id = 0;

    // Add Logic to individual methods , then add those to GUI
    public StackPane Entry(Entry[] array) {
        boolean more = true;
        GridPane grid = new GridPane();
        grid.setAlignment(Pos.CENTER);
        grid.setHgap(15);
        grid.setVgap(15);

        pane.getChildren().clear();
        pane.getChildren().add(grid);

        TextField text1 = new TextField();
        text1.setPromptText("Product Name");
        grid.add(text1, 0, 0);

        TextField text2 = new TextField();
        text2.setPromptText("Product Quantity");
        grid.add(text2, 0, 1);

        TextField text3 = new TextField();
        text3.setPromptText("Product Description");
        grid.add(text3, 0, 2);

        Button buttonAdd = new Button("Add");
        buttonAdd.setMinWidth(text3.getWidth());
        buttonAdd.setOnMouseClicked(e -> {
            Entry item = new Entry();

            item.index = id;
            item.name = text1.getText();
            item.quantity = text2.getText();
            item.notes = text3.getText();
            text1.clear();
            text2.clear();
            text3.clear();
            array[id] = item;
            System.out.println(id);
            countArray();
        });
        grid.add(buttonAdd, 0, 3);

        return pane;
    }

    public StackPane List(Entry[] array) {
        int point;
        Entry op;
        List list = new ArrayList();

        StackPane listPane = new StackPane();
        listPane.setAlignment(Pos.CENTER_LEFT);
        listPane.setPadding(new Insets(15, 15, 15, 15));

        ObservableList<String> seeList = FXCollections.observableList(list);
        ListView viewList = new ListView(seeList);
        viewList.setMaxWidth(250);
        viewList.setMaxHeight(350);
        for (int i = 0; i < array.length; i++) {
            if (array[i] == null) {
                break;
            }
            seeList.add(array[i].name);
        }

        viewList.setOnMousePressed(e -> {
            viewList.getSelectionModel().getSelectedIndex();
            
            GridPane items = new GridPane();
            items.setPadding(new Insets(0, 100, 0, 15));
            items.setAlignment(Pos.CENTER_RIGHT);
            items.add(new Text("Name: " + array[viewList.getSelectionModel().getSelectedIndex()].name), 0, 0);
            items.add(new Text("Quantity: " + array[viewList.getSelectionModel().getSelectedIndex()].quantity), 0, 1);
            items.add(new Text("Notes: " + array[viewList.getSelectionModel().getSelectedIndex()].notes), 0, 2);
            listPane.getChildren().add(items);
            
        });
        
        Button sort = new Button("Sort");
        sort.setAlignment(Pos.CENTER);
        sort.setOnMouseClicked(e ->{
            Collections.sort(seeList);
        });
        pane.getChildren().clear();
        listPane.getChildren().add(viewList);
        pane.getChildren().addAll(listPane, sort);

        return pane;
    }

    public StackPane Find(CharSequence search, Entry[] array) {
        String prodName, name;
        pane.getChildren().clear();
        prodName = search.toString();

        for (int j = 0; j < 200; j++) {

            if ((j == 199) || array[j] == null) {
                pane.getChildren().add(new Text(prodName + " is not in the"
                        + " system."));
                break;
            }

            if (prodName.equalsIgnoreCase(array[j].name)) {
                GridPane items = new GridPane();
                items.setVgap(15);
                items.setAlignment(Pos.CENTER);
                items.add(new Text("Name: " + array[j].name), 0, 0);
                items.add(new Text("Quantity: " + array[j].quantity), 0, 1);
                items.add(new Text("Notes: " + array[j].notes), 0, 2);
                pane.getChildren().add(items);
                break;
            }

        }
        return pane;
    }

    public StackPane Save(Entry[] array, File filew) {
        String name;

        name = "Inv";

        try {
            PrintStream fileWriter = new PrintStream(filew);
            for (int i = 0; i < 200; i++) {
                if (array[i] == null) {
                    break;
                }
                fileWriter.println(array[i].name + " " +
                    array[i].quantity  + " " +
                    array[i].notes);
            }
            fileWriter.close();
        } catch (IOException ex) {

        }
        System.out.println("i \nInventory Stored");

        return pane;
    }

    public StackPane Load(Entry[] array, File filew){
        String readFile;
        
        readFile = filew.getName();

        try{
        Scanner input = new Scanner(new FileInputStream(readFile));
        String name, number, note;

        while (input.hasNext()) {
            name = input.next();
            number = input.next();
            note = input.nextLine();

            Entry item = new Entry(name, number, note);
            array[id] = item;
            id++;
        }

        input.close();
        }catch (FileNotFoundException ex){
            
        }
        System.out.println("Items loaded");
        return pane;
    }

    public StackPane Quit() {
        pane.getChildren().clear();
        pane.getChildren().add(new Text("Quit"));
        return pane;
    }

    private void countArray() {
        id++;
    }
}

class Entry {

    public String name, quantity, notes;
    public int index;

    Entry() {
    }

    Entry(String newName, String number, String newNotes) {
        name = newName;
        quantity = number;
        notes = newNotes;
    }

    String getName() {
        return name;
    }

    void describeObject() {
        System.out.println("\nProduct: " + name);
        System.out.println("Quantity: " + quantity);
        System.out.println("Notes: " + notes);
    }

}
