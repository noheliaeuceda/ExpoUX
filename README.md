# Ejemplo de Redux

Para comenzar con Redux es importante que sepamos 3 cosas:
-Dispatchers
-Actions
-Tipos de Acciones 
-Funcion del store

Para mostrar la lista de elementos, estoy usando React Native ListView que está usando una tienda de Redux (items) como su dataSource.
Hay dos acciones en el reductor; ADD_ITEM y REMOVE_ITEM.

```
// Start the sequence of item ID's at 0
let nextItemId = 0;
// Items reducer
const items = (state = [], action) => {
  switch (action.type) {
    case "ADD_ITEM": {
      return [
        ...state,
        {
          id: nextItemId++,
          name: action.name,
          bgColor: action.bgColor
        }
      ];
    }
    case "REMOVE_ITEM": {
      // Find index of item with matching ID and then
      // remove it from the array by its' index
      const index = state.findIndex(x => x.id === action.id);
      return [...state.slice(0, index), ...state.slice(index + 1)];
    }
    default:
      return state;
  }
};
export default items;
```
ListView requiere un dataSource, y debe volver a procesarse cuando se actualiza el dataSource. 
Para hacer esto, dataSource se asigna a un puntal usando mapStateToPros, y luego le conectamos el Componente.

```
class ItemList extends Component {
  constructor(props) {
    super(props);
    this.handleDestroyItem = this.handleDestroyItem.bind(this);
  }
handleDestroyItem(id) {
    this.props.dispatch({ type: "REMOVE_ITEM", id });
  }
render() {
    return (
      <ListView
        style={styles.container}
        enableEmptySections={true}
        dataSource={this.props.dataSource}
        renderRow={rowData => {
          return (
            <Item
              rowData={rowData}
              handleDestroyItem={id => this.handleDestroyItem(id)}
            />
          );
        }}
      />
    );
  }
}
// Handle data source change from redux store
const dataSource = new ListView.DataSource({
  rowHasChanged: (r1, r2) => r1 !== r2
});
function mapStateToProps(state) {
  return {
    dataSource: dataSource.cloneWithRows(state.items)
  };
}
ItemList.propTypes = {
  dataSource: PropTypes.object,
  dispatch: PropTypes.func
};
export default connect(mapStateToProps)(ItemList);
```

