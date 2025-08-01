#mock
# Simple
>[!info] **Definitions**
>Simple example assumes Mocks were already setup/ defined in the test class
```java 

objectMock = EasyMock.createMock(ObjectType.class);
//Recording
EasyMock.expect(objectMock.function()).andReturn(functionReturnValue);
// do as many time as needed but in order of tests
EasyMock(objectMock);
//do the test
verify(objectMock);

``` 
>[!warning] **Recording order**
>EasyMock functions have to be recorded in the order that they are used in the test or `verify` will fail


# Example
``` java 
import org.easymock.*;  
import org.junit.jupiter.api.*;  
import org.junit.jupiter.api.extension.ExtendWith;  
  
import java.time.LocalDate;  
  
import static org.easymock.EasyMock.*;  
import static org.junit.jupiter.api.Assertions.*;  
  
@ExtendWith(EasyMockExtension.class)  
class DiscussionTest {  
    @TestSubject  
    Discussion discussion = new Discussion();  
    @Mock  
    Course courseMock;  
    @Mock  
    Comment commentMock;  
    @Mock  
    Student student;  
  
    @Test  
    public void testComment() {  
        commentMock = EasyMock.createMock(Comment.class);  
        EasyMock.expect(commentMock.save()).andReturn(Boolean.TRUE);  
        EasyMock.replay(commentMock);  
        int numComments = discussion.getNumberOfComments();  
        assertTrue(discussion.addComment(commentMock));  
        assertEquals(numComments+1, discussion.getNumberOfComments());  
        verify(commentMock);  
  
    }  
    @Test  
    public void testCommentIfSavingFails() {  
        commentMock = EasyMock.createMock(Comment.class);  
        EasyMock.expect(commentMock.save()).andReturn(Boolean.FALSE);  
        EasyMock.replay(commentMock);  
        int numComments = discussion.getNumberOfComments();  
        assertFalse(discussion.addComment(commentMock));  
        assertEquals(numComments, discussion.getNumberOfComments());  
        verify(commentMock);  
    }  
    @Test  
    public void testStartCourseDiscussion() {  
        courseMock = EasyMock.createMock(Course.class);  
        student = EasyMock.createMock(Student.class);  
        EasyMock.expect(courseMock.isDiscussionAllowed(student)).andReturn(Boolean.TRUE);  
        EasyMock.replay(student);  
        EasyMock.replay(courseMock);  
        boolean success = discussion.startCourseDiscussion(courseMock, student, "r");  
        assertTrue(success);  
        assertEquals("r", discussion.getTopic());  
        assertEquals(courseMock, discussion.getCourse());  
        verify(courseMock);  
  
    }  
    // TODO implement the tests  
  
}
``` 
