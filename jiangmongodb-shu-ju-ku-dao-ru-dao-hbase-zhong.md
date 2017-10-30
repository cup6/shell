`import java.io.IOException;`

`import java.net.UnknownHostException;`



`import org.apache.hadoop.conf.Configuration;`

`import org.apache.hadoop.hbase.HBaseConfiguration;`

`import org.apache.hadoop.hbase.client.HTable;`

`import org.apache.hadoop.hbase.client.Put;`



`import com.mongodb.DB;`

`import com.mongodb.DBCollection;`

`import com.mongodb.DBCursor;`

`import com.mongodb.DBObject;`

`import com.mongodb.Mongo;`



`public class MongoHbase {`

`	`

`	public static void main(String args[]){`

`		Mongo mongo = null;`

`		try{`

`			//连接mongo库`

`			mongo = new Mongo("hostAddress");	//e.g：localhost`

`			DB mydb = mongo.getDB("dbName");`

`			DBCollection coll = mydb.getCollection("collectionName");`

`			`

`			//获得数据集中所有文档`

`			DBCursor cur = coll.find();`

`			`

`			//连接hbase，先写配置类`

`			Configuration conf = new Configuration();	`

`			conf.set("hbase.zookeeper.quorum", "hostAddress");	//e.g: 10.10.1.201`

`			conf.set("hbase.zookeeper.property.clientPort", "2181");`

`			HBaseConfiguration hbaseConf = new HBaseConfiguration(conf);`

`			`

`			//HTable用来与hbase表交互`

`			HTable table = null;`

`			try{`

`				//new一个HTable实例，参数为配置类，和表名'person'`

`				table = new HTable(hbaseConf,"person");`

`				`

`				//将刚才在mongo库中获取的数据依次循环出来，并写入hbase库`

`				for(int i=1;cur.hasNext();i++){`

`					//DBObject用来存放一个mongo文档`

`					DBObject obj = cur.next();`

`					//Put是hbase中的数据结构，用来封装要插入表中的数据，构造方法有许多，这里使用的是指定插入行键的构造方法`

`					Put put = new Put(new String("row" + i).getBytes());`

`					`

`					//向Put中加入待插入的数据，该方法也有几个重载版本，此处用的参数为：列族名，成员名，值，注意参数必须是字节数组`

`					put.add("info".getBytes(), "name".getBytes(), ((String)obj.get("name")).getBytes());`

`					put.add("info".getBytes(), "age".getBytes(), ((String)obj.get("age")).getBytes());`

`					`

`					//将数据插入hbase表中`

`					table.put(put);`

`				}`

`				`

`			}catch(IOException e){`

`				System.out.println(e.getMessage());`

`				e.printStackTrace();`

`			}finally{`

`				if(table != null){`

`					try{`

`						table.close();	//HTable使用完毕以后，务必将其关闭以释放相关资源`

`					}catch(IOException e){`

`						System.out.println(e.getMessage());`

`						e.printStackTrace();`

`					}`

`				}`

`			}`

`			`

`		}catch(UnknownHostException e){`

`			System.out.println(e.getMessage());`

`			e.printStackTrace();`

`		}finally{`

`			if(mongo != null){`

`				mongo.close();	//关闭mongo连接`

`			}`

`		}`

`	}`

`}`

