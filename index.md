## Real world use case of mutable keyword in C++.

For introduction, you can use `mutable` keyword to mark member variables which can then be modified from `const` member methods as well.

I never actually used this until I came across the below use case.

**Consider there is a Lock class which exposes interfaces to lock the underlynig resource with exclusive access for writes or with shared access for read access.**
```
Class Lock
{
  ...
  public:
  LockExclusive(){...}
  LockShared(){...}
  ReleaseLock(){...}
};
```
**And you have an encapsulator for a very important resource.**
```
class ProtectMyResource
{
  private:
  int VeryVeryPrivateResource;
  Lock AbstractedLock;
  //Other members.
  
  public:
  void SetResource(int NewValue)  //This member is obviously modifying members and the intent is clear as well, so not marked as const.
  {
    AbstractedLock.LockExcluive();
    VeryVeryPrivateResource = NewValue;
    AbstractedLock.ReleaseLock();
  }
  
  void GetResourceValue(int &BackDoor) const //There is no intent to modify the members within this method, so marked as const.
  {
    AbstractedLock.LockShared();
    BackDoor = VeryVeryPrivateResouce;
    AbstractedLock.ReleaseLock();
  }
};
```

 All well, but this does not compile. Since `GetResourceValue()` is marked as `const` and it is modifying member variables.
 `AbstractedLock.LockShared()` when called is going to make some changes to the underlying structure which breaks const.

Now you could either not mark `GetResourceValue()` as `const`, but then the intent of not modifying the members through this function is lost.

Or you could mark `AbstractedLock` as `mutable`.

```
mutable Lock AbstractedLock;
```
