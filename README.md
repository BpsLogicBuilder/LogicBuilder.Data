# LogicBuilder.Data

## Purpose

LogicBuilder.Data provides the foundational base class (`BaseData`) for all data entities in the data access layer. This library is designed to work seamlessly with Entity Framework Core to enable generic CRUD operations and state tracking across your application's data stack.

## Key Components

### BaseData Class

The `IBaseData` interface and `BaseData` abstract class serve as the base for all Entity Framework POCO (Plain Old CLR Object) classes in your data layer. All data entities should inherit from this class to leverage the framework's generic repository pattern and state management capabilities.

### EntityState Property

The `EntityState` property of type `EntityStateType` tracks the current state of each entity:
- **Added** - Entity is new and should be inserted into the database
- **Modified** - Entity exists and has been changed
- **Deleted** - Entity should be removed from the database  
- **Unchanged** - Entity exists but has not been modified

## Integration with LogicBuilder.EntityFrameworkCore.SqlServer

The `BaseData` class is essential for the generic repository pattern implemented in `LogicBuilder.EntityFrameworkCore.SqlServer`. The framework uses the `EntityState` property to:

1. **Track Entity Changes** - The `LogicBuilder.EntityFrameworkCore.SqlServer` library reads the `EntityState` property to determine which repository operation to perform (Insert, Update, Delete, or no action).

2. **Graph Operations** - When saving entity graphs (entities with related child entities), the framework traverses the entire object graph and applies state changes based on each entity's `EntityState` value.

3. **State Conversion** - The `LogicBuilder.EntityFrameworkCore.SqlServer` library converts the custom `EntityStateType` enum values to Entity Framework's native `EntityState` values when persisting changes.

4. **Repository Pattern** - All generic repositories and stores constrain their type parameters to classes that inherit from `BaseData`, ensuring consistent state tracking across all data operations.

## Usage Example

```csharp
public class StudentData : BaseData
{
    public int StudentId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}

// When creating a new entity
var student = new StudentData 
{ 
    FirstName = "John",
    LastName = "Doe",
    EntityState = EntityStateType.Added 
};

// When modifying an existing entity
existingStudent.FirstName = "Jane";
existingStudent.EntityState = EntityStateType.Modified;

// When deleting an entity
studentToDelete.EntityState = EntityStateType.Deleted;
```

By inheriting from `BaseData`, your entities automatically participate in the framework's change tracking and can be used with all generic repository methods throughout the LogicBuilder ecosystem.